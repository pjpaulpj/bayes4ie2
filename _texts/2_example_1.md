---
layout: post
title: Getting started with Stan - a simple example
date: 2016-03-22 14:40:45
---

For all of the examples in this website, we will use the following simple workflow. This is what we mean when we say that we do “Bayesian inference with Stan and python.”

1.	*Define a statistical model in Stan*  
	The core of this model is the prior and likelihood that I talked about in the previous example.
2.	*Pass data to the Stan model and run the model* 
	As mentioned earlier, we will use python to manipulate data and pass it to Stan. After we run it, the model will output a set up samples of the parameters from the posterior distribution.
3.	*Use these samples to perform inference on the parameters*

I’ll explain each of these steps in more detail below, but first I offer a few quick disclaimers. 

### A few disclaimers
Note that in real world applications you will probably want to add a few steps to this workflow. First, you will probably want to test out a first different models and make sure that they work on some simulated data before selecting a final model.  Second, you will also want to conduct power calculations using your selected model prior to conducting the impact evaluation.  Third, you will want to check that your model has “converged” before using the output from the model.  Lastly, in Stan, you compiling the statistical model and running the model are actually separate steps.  In the interest of simplicity, in the examples here we often perform model compilation and model running at the same time, but if compilation takes a long time and you run the same model more than once with different data you may want to separate these steps.  

Now that I’ve offered those disclaimers, I’m going to ignore them for most of the rest of this course but do keep them in mind before using this in the real world.  (I’ll come back to these points at the end of the course and provide more guidance on each.)


## A simple example 
Let’s start by getting our hands dirty with a simple example.  Returning to the example from the introduction, let’s suppose we observe 10 tosses from a coin which we don’t whether is fair or not. In the previous example, we assumed that there were only two possible values for \theta, the probability that the coin lands heads up: fair, or .5, and rigged, or .75. Let’s now assume that we don’t really know anything about theta and thus we assume that there is an equal probability that theta is any value between 0 and 1.  A distribution that conforms to this assumption is the beta(1,1) distribution. (This is just a uniform distribution from 0 to 1.)


### Step 1 – define the statistical model in Stan
The code to define such a model in Stan is below. (Note that this code comes from Carpenter et al.)

```python
Stan_code = """
data {
  // the data block tells Stan what data we will pass it from python
  int<lower=0> N; // tell
  int<lower=0,upper=1> y[N];
}

parameters {
  // the paramaters block defines the parameters in the statistical model
  // in Stan, all variables should be either data or parameters (or derived from one or the other)
  // Stan attempts to draw samples from the posterior of the parameters
  real<lower=0,upper=1> theta;
}

model {
  // the model block is the meat of the statistical model
  // the model block should define your prior and your likelihood
  theta ~ beta(1,1);
  y ~ bernoulli(theta);
}
"""
```

### Step 2 – pass the model some data and run the model
Next, we pass the model some data and run the model:

``` python
% matplotlib inline
# the following line imports the pystan package
import pystan

# set the data in python
N = 10
y = [0,1,0,0,0,0,0,0,0,1]
stan_data = {'N': N, 'y': y}

# pass the data to the model and run the model
fit = pystan.stan(model_code=stan_code, data=stan_data, iter=1000, chains=4)
```

### Step 3- Perform inference on the parameters using the Stan output
Stan outputs a set of samples from the posterior of our parameters.  The cool thing about this is that it allows us to conduct any inference we want on our parameters. For example, for the example above, we could estimate the probability that \theta is above .5 by looking at the percentage of samples of theta that are greater than .5.  For most frequentist analyses, in order to test a hypothesis like this, we would have to assume that our parameters are normally distributed.  

First, let’s take a look at the distribution of our samples of theta

```python
fit.traceplot()
```
![](/assets/binom_traceplot.png)

Next, let’s estimate the probability that theta is greater than .5. To do this, I first extract the samples using the “extract()” method.

```python
temp = fit.extract()['theta']
sum(temp>.5)/len(temp)
```

## A more general overview of Stan
The example was really simple but hopefully gave you a rough idea of how to program a statistical model in Stan.  You probably also noticed that comments are set off with “//”

Stan allows for much more complex models of course.  I won’t go into all the details of Stan (there is a 554 page manual for that purpose) but will give a quick overview of some of the basic concepts that are important to understand in Stan.

### Code blocks
Code is separated into different blocks.  The model above included only the data, parameters, and model block but there are four other blocks that it is possible to define (functions, transformed data, transformed parameters, and generated quantities).  We will stick to the three main blocks for the most part. 

Blocks must be in order.

### Variables
In Stan, parameters and data must be defined as variables with a specific type. (This is a bit of a pain but allows Stan to be really fast.)  Read the Stan manual section on variables to get the full details of all the variables types, but here is a whirlwind tour:
1. There are four basic **data types**: 
		1. **int** - for integers
		2. **real**  - for continuous variables
		3. **vector** – for a column vector of reals
		4. **matrix** – for a matrix of reals
2. You can (and should!) add **constraints** to variables
	1. For example  “int<lower=0, upper=1> y” tells Stan that y is either 0 or 1.
	2. Constraints really help speed Stan up so use them wherever possible
3. You can create **arrays** of variables
	1. For example “int y[10]” tells stan that y is an array of 10 integers
	2. Note that this also works with vectors and matrices. (i.e. you can create an array of 10 vectors each of length n)
	3. Whether to use a vector or a n-dimensional array of reals is a tricky question.  



