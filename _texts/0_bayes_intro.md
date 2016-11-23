---
layout: post
title: A short introduction to Bayesian inference
date: 2016-01-22 14:40:45
---


*Bayesian inference is an approach to generating probabilistic statements about the world by combining a prior belief about the world with evidence based on data.*  

That’s a lot of information for one sentence, so let me break it down in parts using an example.  Suppose that we are trying to estimate the probability that a coin is rigged. Prior to seeing the coin used, we believe that there is a 50% chance that the coin isn’t rigged (i.e. has an equal chance of landing heads or tails up) and a 50% chance that the coin is rigged and will land heads up ¾ of the time.  (To simplify things, I am assuming that the coin can only be rigged in a certain way. Of course, the coin could be rigged in such a way that the probability of a heads is not equal to ¾.)  In the language of Bayesian inference, our beliefs about the probability of the rigged coin constitute our **pior** that the coin is fair. We then observe the coin being tossed 5 times with heads coming up 4 out of 5 times. Using the binomial distribution, we know that the probability of getting heads 4 out of 5 times is 

$$  p(y=4) = {5 \choose 4} \theta^4*(1-\theta) $$

Where \\(\theta \\) is the probability of the coin landing heads up.  In Bayesian terminology, this is the **likelihood** function. To combine the prior and the likelihood function, we use Bayes’ rule.  

$$ p(\theta| y) = \frac{p(y|\theta)p(\theta)}{p(y)} $$

For example, we can use Bayes’ rule to calculate the probability that the coin is fair.

$$ p(\theta = .5 | y=4) = \frac{p(y|\theta)p(\theta)}{p(y)} = \frac{p(y|\theta)p(\theta)}{\sum{p(y|\theta)p(\theta)}} $$	

{% raw %}
$$ = \frac{{5 \choose 4}*.5^4*.5*.5}{{5 \choose 4}*.5^4*.5*.5 + {5 \choose 4}*.75^4*.25*.5} = 0.28 $$
{% endraw %}

Thus, after seeing the data, we revise downward our belief that the coin is fair from .5 to .28.  This final probability is our **posterior**.  

## The two main requirements for Bayesian inference
From the above example and from inspecting Bayes’ rule, we can see that in order to conduct Bayesian inference, we always need a) a **prior** and b) a **likelihood**, or a full probabilistic model of our data given our parameters.  At a practical level, these two requirements are really what drive the difference between a Bayesian approach and a "frequentist" approach.  (If you haven’t heard the term “frequentist” that’s probably because this approach is so dominant in academia that most people simply think of it as the approach to doing statistics.  But just like you’ve been speaking in prose without even knowing it, you’ve probably been conducting frequentist inference without even knowing it.) In frequentist inference, we don't have a prior.  In addition, while we may use a likelihood function, that is not a necessity. (There are many other methods of estimating parameters.)

We can also see that the final output from our analysis is a probability density for our parameters of interest.  This may not seem that out of the ordinary, but again let’s compare that with the output from an analysis based on frequentist inference.   As we all remember from our first stats course, a p-value of .05 does not mean that there is a 95% probability that our alternate hypothesis is correct.  Rather, a p-value of .05 for a test statistic means that, if the null hypothesis were true and we conducted this exact impact evaluation many, many times, the test statistic would be as large or larger than the value we obtained approximately 5% of the time. Thus, this approach to inference is called “frequentist” because it relies on this notion of the probability of an event as the limiting frequency of the event occurring under repeated trials. 

In contrast, Bayesians interpret probability as the fundamental uncertainty about a statement’s truth. In the example above, obviously the coin is either rigged or not.  To a Bayesian, it nevertheless makes sense to talk about the “probability” that the coin is rigged in the sense that we have a fundamental uncertainty as to whether the coin is rigged or not.


