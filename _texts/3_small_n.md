---
layout: post
title:  From p-values to probabilities
date: 2016-04-22 14:40:45
categories: jekyll update
---

What do you do when you run an evaluation and your estimate of the treatment effect is large but the p-value is only 0.23? 0.23 is better than 0.5, but then again it is quite a bit larger than the typically used thresholds of 0.05 and 0.1. 

Of course, what we, and the client, really want is not a p-value but a probability.  For example, let's suppose that there is another intervention that costs about the same and which we think has an effect of \\(\delta\\).  In that case, what we really want to know is the probability that the intervention we are evaluating has an effect greater than \\(\delta\\). I illustrate how to do this using a simple example.


## Input some fake data
First, let's input some fake data for a binary treatment and a binary outcome. Assume that this data came from a randomized experiment.

```python
# Create two vectors of fake data: one for a binary outcome and one for a binary treatment
import numpy as np
outcome = np.array([1, 1, 0, 1, 0, 1, 1, 1, 1, 0, 1, 0, 0, 0, 0, 1, 0, 1, 1, 1, 1, 0, 1,
       0, 1, 0, 0, 0, 0, 1, 1, 0, 1, 0, 1, 1, 0, 0, 1, 1, 0, 1, 0, 1, 0, 0,
       1, 1, 1, 1, 0, 1, 1, 1, 1, 1, 1, 0, 1, 1, 0, 1, 0, 1, 1, 1, 1, 1, 1,
       0])
treat_status = np.array([0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
       0, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,
       1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,
       1])
```

## Run a logit on the fake data
Next, let's run a standard logit on this data. Note that when we run the logit, the estimate of the treatment effect is reasonable large but the p-value is only 0.23.  (Note that the following code is equivalent to running "logit outcome treat_status" in Stata.)


```python
import statsmodels.api as sm
X = sm.add_constant(treat_status)
model = sm.GLM(outcome, X, family = sm.families.Binomial())
print(model.fit().summary())
```

                     Generalized Linear Model Regression Results                  
    ==============================================================================
    Dep. Variable:                      y   No. Observations:                   70
    Model:                            GLM   Df Residuals:                       68
    Model Family:                Binomial   Df Model:                            1
    Link Function:                  logit   Scale:                             1.0
    Method:                          IRLS   Log-Likelihood:                -45.951
    Date:                Sun, 31 Jul 2016   Deviance:                       91.902
    Time:                        11:09:16   Pearson chi2:                     70.0
    No. Iterations:                     6                                         
    ==============================================================================
                     coef    std err          z      P>|z|      [95.0% Conf. Int.]
    ------------------------------------------------------------------------------
    const          0.1335      0.366      0.365      0.715        -0.584     0.851
    x1             0.5974      0.498      1.200      0.230        -0.378     1.573
    ==============================================================================


## Step 1 - Define the statistical model in Stan
The code below fits a Bayesian logit model to this data.  The statistical model that we define in Stan combines a typical logit likelihood with normal priors for the parameters for the logit model.  In this case, my prior is that the intercept for the logit model (alpha) is distributed normally with mean 0 and standard deviation 10 and the slope for the logit model (beta) is also distributed normally with mean 0 and standard deviation 10.  In other words, ex-ante, I think that there is a 50/50 chance that the intervention has a positive effect.  

Note that this model uses the "generated quantities" code block.  This is because the quantity that I really care about is not alpha or beta but rather the probability of success.  The generated quantities block allows you to define a quantity that will be calculated for each sample. (Note that I could also do this calculation in python after generating samples for alpha and beta but I find it easier to perform this calculation in the Stan code.)

```python
import pystan
stan_code = '''
data {
    int<lower=0> N;  // number of observations
    int<lower=0,upper=1> y[N];  // outcome data
    vector[N] treat;  // variable indicating treatment arm for each observation
}
parameters {
    real alpha;
    real beta;
}
model {
    alpha ~ normal(0,10);  // our prior for alpha
    beta ~ normal(0,10); // our prior for beta
    y ~ bernoulli_logit(alpha + beta*treat);
}    
generated quantities {
    real effect;
    effect <- inv_logit(alpha+beta)-inv_logit(alpha);
}
'''
```

## Step 2 - Pass data to the Stan model and run the model 
The code below passes the fake data that we generated in the beginning to the Stan model and ouptputs samples from the posterior for alpha and beta. 

```python
stan_data = {'N': len(outcome),
        'y': outcome,
        'treat': treat_status }
fit = pystan.stan(model_code=stan_code, data=stan_data, iter=1000, chains=4)
```

## Step 3 - Perform inference on alpha and beta
Now that we have fit the model, we want to use the results to estimate the probability that the effect of the intervention is greater than \\(\delta\\).  

Let's first extract the vector for the posterior values and graph the values using a histogram. 


```python
% matplotlib inline
import matplotlib.pyplot as plt
import seaborn as sns

effect_sim = fit.extract()['effect']
sns.distplot(effect_sim)
```

![]({{site.url}}/assets/dist_effect_size.png)


Next, let's calculate the probability that the effect is greater than \\(\delta\\).  For this example, let's assume \\(\delta\\) is equal to .05.  In other words, the other intervention we are comparing our intervention to causes a 5 percentage point increase in the probability of the outcome being a success. 


```python
# calculate the probability that effect size is greater than 0
(effect_sim >0.05).sum()/len(effect_sim)
```
    0.75849999999999995



## Takeaway
According to our model, the probability that the effect of the new intervention is greater than the intervention that we are comparing it to is 76%! Despite the large p-value for our initial analysis, the more thorough Bayesian shows that our message to the client should be clear: you should definitely take up this intervention.  

I should add one last caveat. In this example, a relatively large p-value ended up translating into a relatively large probability that the intervention is effective.  Please note that this is not always going to be the case!  