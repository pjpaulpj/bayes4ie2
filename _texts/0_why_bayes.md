---
layout: post
title: A short introduction to Bayesian inference
date: 2016-01-22 14:40:45
---


Bayesian inference is an approach to generating probabilistic statements about the world by combining a prior belief about the world with evidence based on data.  

That’s a lot of information for one sentence, so let me break it down in parts using an example.  Suppose that we are trying to estimate the probability that a coin is rigged. Prior to seeing the coin used, we believe that there is a 50% chance that the coin isn’t rigged (i.e. has an equal chance of landing heads or tails up) and a 50% chance that the coin is rigged and will land heads up ¾ of the time.  In the language of Bayesian inference, our beliefs about the probability of the rigged coin constitute our **pior** on the probability of a heads or the coin. We then observe the coin being tossed 5 times with heads coming up 4 out of 5 times. Using the binomial distribution, we know that the probability of getting heads 4 out of 5 times is 

$$  p(y=4) = {5 \choose 4} \theta^4*(1-\theta) $$

In Bayesian terminology, this is the **likelihood** function. To combine the prior and the likelihood function, we use Bayes’ rule.  

$$ p(\theta| y) = \frac{p(y|\theta)p(\theta)}{p(y)} $$

We can use Bayes’ rule to calculate the probability that the coin is fair

$$ p(\theta = .5 | y=4) = \frac{p(y|\theta)p(\theta)}{p(y)} = \frac{p(y|\theta)p(\theta)}{\sum{p(y|\theta)p(\theta)}} $$	

{% raw %}
$$ = \frac{{5 \choose 4}*.5^4*.5*5}{{5 \choose 4}*.5^4*.5*5 + {5 \choose 4}*.75^4*.25*5} = 0.28 $$
{% endraw %}

Thus, after seeing the data, we revise downward our belief that the coin is fair from .5 to .28.  This final probability is our **posterior**.  

From the above example and from inspecting Bayes’ rule, we can see that in order to conduct Bayesian inference, we always need a) a **prior** and b) a **likelihood**, or a full probabilistic model of our data given our parameters.  At a practical level, these two requirements are really what drive the difference between a Bayesian approach and a "frequentist" approach.  (If you haven’t heard the term “frequentist” that’s probably because this approach is so dominant in academia that most people simply think of it as the approach to doing statistics.  But just like you’ve been speaking in prose without even knowing it, you’ve probably been conducting frequentist inference without even knowing it.) In frequentist inference, we don't have a prior.  In addition, while we may use a likelihood function, that is not a necessity. (There are many other methods of estimating parameters such as GMM or empirical likelihood.  Alternatively, we might use a likelihood function but only expect it to get part of the distribution right as in quasi and pseudo maximum likelihood.)

We can also see that the final output from our analysis is a probability density for our parameters of interest.  This may not seem that out of the ordinary, but again let’s compare that with the output from an analysis based on frequentist inference.   As we all remember from our first stats course, a p-value of .05 does not mean that there is a 95% probability that our alternate hypothesis is correct.  Rather, a p-value of .05 for a test statistic means that, if the null hypothesis were true and we conducted this exact impact evaluation many, many times, the test statistic would be as large or larger than the value we obtained approximately 5% of the time. Thus, this approach is called “frequentist” because it relies on this notion of the probability of an event as the limiting frequency of the event occurring under repeated trials. 

In contrast, Bayesians interpret probability as the fundamental uncertainty about a statement’s truth. In the example above, obviously the coin is either rigged or not.  To a Bayesian, it nevertheless makes sense to talk about the “probability” that the coin is rigged in the sense that we have a fundamental uncertainty as to whether the coin is rigged or not.

A More Practical Example
-------
*(Note to reviewers: Let me know if you find the following example useful or just redundant.)*

To provide a more realistic example, and to hopefully convince you that Bayes’ rule is practically important as well as theoretically interesting, I’ll use Bayes’ rule to estimate the probability that Heather had (has?) chikungunya.  This example also helps illustrate the relative advantages of Bayesian and frequentist inference and when you might want to use each.

As many of you know, about three weeks ago (from the date this post was written) Heather fell ill with a fever, a rash, and really bad joint pain.  To a non-medically trained observer like myself, Heather’s illness appeared to be a textbook case of chikungunya.  Heather received a test for chikungunya but it came out negative. After the negative test, I wanted to estimate the probability that Heather had chikungunya.  (Note that I am using the term probability in the Bayesian sense.  Heather either has chikungunya or she doesn't, but we are uncertain of this.)

I did a bit of googling on the test that Heather was administered and found that the "sensitivity" of the test, or the probability of a positive result given a patient has the disease, is 90% and the "specificity" of the test, or the probability of a negative result give a patient does not have the disease, is 80%. (Actually, I am rounding these figures down quite a bit.  The actual reported sensitivity and specificity for the test are much higher. Based on my personal experience and the doctors I talked to, the reported figures are likely unrealistic.) We can use Bayes' rule to estimate the probability that Heather has chikungunya as follows.  I denote Heather's chikungunya status using the notation H+/- (where + indicates she has the disease and - indicates she doesn't) and Heather's test status using the notation T+/-.

$$ p(H+ | T-) = \frac{p(T-|H+)p(H+)}{p(T-)} = \frac{p(T- | H+)p(H+)}{ p(T- | H+)p(H+) + p(T- | H-)p(H-)} $$

From our googling, we know that p(T+\|H+)=.9 and p(T-\|H+)=.8 and, based on her original symptoms, my prior of Heather having chikungunya was p(H+)=.9.  Thus, I estimate the probability of Heather having chikungunya, after the negative test result, at around 53%.  

This example also illustrates the relative strengths and weaknesses of Bayesian and frequentist inference.  In estimating the probability that Heather had chikungunya, I used Bayesian inference because the ultimate goal of the analysis was to allow Heather to provide useful information to Heather on what to do about her symptoms.  At the same time, the figures that I used for the sensitivity and specificity of the test were probably estimated using frequentist inference using (I assume though don't know for sure) a procedure regulated by the FDA.  In that case, it likely makes sense for the FDA to regulate that the test makers use a frequentist procedure.  The FDA is responsible for ensuring the overall accuracy of the reported stats from these test makers and thus the "frequentist coverage properties" -- i.e. the proportion of test results that are within the reported accuracy bounds -- is likely the FDA's overall goal. 


Additional Resources for Understanding Bayesian Inference
--------

1.	[Principles of Multilevel Modeling](https://bcpsqc.ca//documents/2012/11/hierarchical-modeling-greenland.pdf) by Sander Greenland
	If you only read one paper about Bayesian inference, then this should be it.  Short and readable.
2.	[Bayesian Adaptive Methods for Clinical Trials](https://www.amazon.com/Bayesian-Adaptive-Methods-Clinical-Biostatistics/dp/1439825483) by Berry et al. 
	This book contains a great, very readable introduction to Bayesian inference.  The rest of the book discusses the use of Bayesian inference for randomized controlled trials but caters to a bio-medical researcher audience.  Available in the IDinsight Delhi library. 
3. [Bayesian Data Analysis](http://www.amazon.in/Bayesian-Analysis-Chapman-Statistical-Science/dp/1439840954) by Gelman et al
	This is considered the standard textbook on Bayesian analysis.  This book is Ok, but frankly I would recommend the resources above first.  Available in the IDinsight Delhi library.
