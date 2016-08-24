---
layout: post
title: A (very) short introduction to Bayesian analysis
date: 2016-01-22 14:40:45
---

- start with an explanation of Bayes theorem
- tell readers that most explanations of Bayes then go on to show how to estimate a prior with a normal prior and a normal model and discuss stuff like conjugate priors
	-- the software these days is good enough that you can get by without really digging too much into the details
	-- or, at least, you can start using Bayesian analysis and then brush up on the details later


When analyzing data from our evaluations, we typically use a "frequentist" or "null hypothesis testing" approach.  If you haven't heard the term "frequentist" that's probably because this approach is so dominant in academia that most people simply think of it as *the* approach to doing statistics. 

There is an alternative approach to doing statistics though: the Bayesian approach.  In this blog post I hope to show that Bayesian analysis may be particularly useful for the work we do by going through the use of a simplified example application inspired by a recent proejct.  



## Why Bayes?
Bayes vs frequentist is a hotly contested debate so take this with a grain of salt but here's my take. Bayesian analysis is more useful when the purpose of the analysis is to inform a specific decision by a specific actor. This is because Bayesian analysis let's you take the actor's prior beliefs into account and the output from analysis tells the actor exactly what decision to make (i.e. -- you can just tell the client "you should do X" rather than trying to explain what a p-value is). You may be asking "if this is true, why is frequentist analysis the dominant paradigm?" This is because frequentist analysis is more useful when there are a lot of people performing a lot of evaluations and the purpose of each analysis is to convince the larger community that the results are not due to chance.  Thus, while frequentist analysis is a reasonable approach in academia and for knowledge focused evaluations I think that Bayesian analysis is more useful for decision focused evaluations. 

Here's XKCD on the difference between the two approaches:  

![title](http://imgs.xkcd.com/comics/frequentists_vs_bayesians.png)


- mention that we will probably deal mainly with models of the from P(Y | X theta)

