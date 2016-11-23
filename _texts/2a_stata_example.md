---
layout: post
title: A simple example in Stata
date: 2016-03-22 14:40:45
---

## Background: An IDinsight Evaluation with Large Effects but Low Statistical Significance
In 2015, IDinsight, in collaboration with CHAI and the government of Zambia, conducted a randomized controlled trial to test the effect of integrating early infant HIV with an existing immunization program in rural Zambia.  Given the high coverage of the immunization program, it was hoped that offering early infant HIV testing when children were immunized would increase testing rates at relatively low cost. The evaluation [found](http://journals.plos.org/plosone/article?id=10.1371/journal.pone.0141455) that offering HIV testing during immunization led to a 16.6% increase in HIV testing rates among infants, a substantial increase.  Yet, the p-value for this result was only 0.26.  

What should the IDinsight team have done in this situation?  0.26 is better than 0.5, but then again it is quite a bit larger than 0.05 and 0.1, the thresholds that we typically use.    

The problem here is that what the team needed was a not a p-value but a probability.  For example, let's suppose that there was another intervention which cost about the same and which the team believed increased early infant HIV testing by \\(\delta\\).  If the team had an estimate for the probability that the effect of program combining testing with immunization was greater than \\(\delta\\) the team could make an informed recommendation to the Ministry as to which program to go forward with.  


## A Simple Example Loosely Based on the CHAI Evaluation
In the remainder of this section I'll demonstrate how Bayesian analysis can help us deal with this problem.  The original RCT used a clustered, three arm design but here I am going to assume that there were only two treatment arms (control and treatment) and that treatment was randomized at the individual infant level in the interest of simplicity.  I have mocked up some fake data for this example [here][1].  


First, let's confirm that the p-value of the effect is .26 if we perform a conventional frequentist analysis of the data.  Sure enough, regressing the variable "outcome" on the variable "treat" yields an estimate for the treatment effect of 8.9 percentage points but a p-value of .265.


## A Bayesian Analysis of the Simple Model

- 







- Starting in version 14, Stata has included some basic functionality to perform Bayesian analysis
- --> the built-in functionality is rather limited, but, it is useful for learning. also 



[1]:{{site.url}}/assets/example_1.dta.
