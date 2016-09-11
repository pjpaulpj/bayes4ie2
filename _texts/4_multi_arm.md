---
layout: post
title:  Choosing the best treatment arm(s)
date: 2016-05-22 14:40:45
categories: jekyll update
---

I am currently involved in a five arm RCT for a client here in India.  The main purpose of this evaluation is help the client, an NGO, determine which of the five interventions they should drop and which they should continue to implement.  In order to make this decision, the client wants to know the probability of each treatment arm having the largest effect.  Unfortunately, it's hard to give a straight answer to that question using a frequentist approach.  We can answer questions like "what is the probability that we would see data this extreme if treatment arm 1 actually has no effect" or "what is the probability that we would see data as extreme as this if treatment arm 1 and treatment arm 2 actually have the same effect" but it's very difficult to estimate the probability of each treatment arm having the largest effect.

As we'll see, Bayesian inference allows us to directly answer the client's question.

## Generate some fake data
Unfortunately, I can't share real data from this evaluation so I will generate some fake data instead. To generate some fake data, I assume the true data generating process for the RCT is...

$$ y_i \sim N(\tau_{j[i]},\sigma_y^2) $$

Where i indexes households, j indexes treatment arms, and j = 0 indicates the control group. I pick arbitrary values for the parameters.  The code for generating this data in Python is included below. 


```python
# import numpy -- this is a python "package" for linear algebra
import numpy as np

# Initialize random number generator
np.random.seed(123)

# Set values for the various parameters in the data generating equation. 
sigma = 1
tau = [.1,.5,1.6,2,0,-0.7]

# Size of each treatment arm
size_arms = np.array([20, 10, 10, 20, 20, 15])

# generate a vector indicating the arm for each observation. 
# I add 1 because array indices start with 0 in python but 1 in Stan
arm = np.repeat(np.arange(6),size_arms) + 1

# Simulate outcome variable
Y = np.repeat(tau, size_arms)+ np.random.randn(sum(size_arms))*sigma
```


```python
# Graph this data to make sure it looks reasonable
%matplotlib inline
import matplotlib.pyplot as plt
import seaborn as sns
# this next line gives the graph the XCD feel
plt.xkcd()
start = 0
for size in size_arms:
    end = start + size
    sns.distplot(Y[start:end], hist = False)
    start = end
```

![](/assets/test_dist.png)

This appears to be about what we would expect give our effect sizes.

## Define the model in Stan and pass 
For this example. I'm going to use a model very similar to the data generating model to fit the data.  This is cheating a little bit, but it makes things a little simpler.


```python
# import the pystan package.  this package allows python to call the Stan language
import pystan
# The next few lines define the model in Stan.  The entire Stan model is saved as the python variable
# stan_code which I use later to fit the model. 
stan_code = '''
data {
    int<lower=0> N;  // number of observations
    int<lower=1> J;  // number of treatment arms
    real y[N];  // outcome data
    int<lower=1,upper=J> jj[N];  // variable indicating treatment arm for each observation
}
parameters {
    real tau[J];
}
model {
    tau ~ normal(0,10);  // Assign tau a multivariate normal prior with no correlations.  
    // the above two statements shows how Stan automatically "vectorizes" operations where appropriate
    for (n in 1:N)
        y[n] ~ normal(tau[jj[n]], 5);
}
'''
stan_data = {'N': sum(size_arms),
        'J': len(size_arms),
        'y': Y,
        'jj': arm }

```


```python
fit = pystan.stan(model_code=stan_code, data=stan_data, iter=1000, chains=4)
```

## Perform inference on samples form the posterior to answer the client's question
Again, as in previous examples, having access to the complete posterior distribution allows us to perform rich anlaysis on the parameters. For example, for each draw from the posterior distribution, we can look at which of the arms has the largest effect (i.e. which element of the vector $\tau_j$ is the largest).  This gives us the probability that each of the treatment arms is the "best" -- what the client originally wanted.  


```python
# pandas is another python package that is really useful for manipulating datasets
import pandas as pd
# get the full set of draws from the posterior for tau and store this in tau_sim
tau_sim = fit.extract()['tau']
# for each of the draws from the posterior, identify which element of tau is the largest
results =  pd.DataFrame({'max':np.argmax(tau_sim, 1)})
# for each element of tau, total up the number of times when that element is the largest
results['max'].value_counts()/2000
```


    3    0.6285
    2    0.1850
    1    0.1110
    0    0.0325
    5    0.0255
    4    0.0175
    Name: max, dtype: float64



Voila!  We see that our model estimates treatment arm 3 (the actual best arm) as being the best with 62.6% probability.  Of course, we cheated a little by using a data generating model that was the same as our estimation model.
