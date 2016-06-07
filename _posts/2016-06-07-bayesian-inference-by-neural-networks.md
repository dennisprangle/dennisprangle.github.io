---
layout: post
title: "Bayesian inference by neural networks. Part 1: background"
description: ""
category: research
tags: [ABC, Likelihood-free, Neural networks]
---
{% include JB/setup %}

Recently George Papamakarios and Iain Murray published [an arXiv preprint](http://arxiv.org/pdf/1605.06376.pdf) on likelihood-free Bayesian inference which substantially beats the state-of-the-art performance by a neat application of neural networks.
I'm going to write a pair of blog posts about their paper.
First of all this post summarises background material on likelihood-free methods and their limitations.
The [second post](/research/2016/06/07/bayesian-inference-by-neural-networks2) will review and comment on the new paper.
Over the next few months I'm hoping to spend some time experimenting with their method,
and I might post about my experiences in implementing it, if it's amenable to short blog posts!

## Intractable likelihoods

*Model-based statistics* assumes that the observed data (e.g. deaths from an infectious disease) has been produced from a random distribution or *probability model*.
The model usually involves some unknown *parameters* (e.g. controlling rates of infection and death for a disease).
*Statistical inference* aims to learn the parameters from the data.
This might be an end in itself - if the parameters have interesting real world implications we wish to learn - or as part of a larger workflow such as prediction or decision making.

Classical approaches to statistical inference are based on the probability (or probability density) of the observed data $$y_0$$ given particular parameter values $$\theta$$.
This is known as the *likelihood function*, $$\pi(y_0|\theta)$$.
Since $$y_0$$ is fixed this is a function of $$\theta$$ and so can be written $$L(\theta)$$.
Approaches to inference involve optimising this, used in [maximum likelihood](https://en.wikipedia.org/wiki/Maximum_likelihood) methods, or exploring it, for [Bayesian](https://en.wikipedia.org/wiki/Bayesian_statistics) methods, which are described in more detail shortly.
A crucial implicit assumption of both approaches is that it's possible and computationally inexpensive to numerically evaluate the likelihood function.

As computing power has increased over the last few decades, there are an increasing number of interesting situations for which this assumption doesn't hold.
Instead models are available from which data can be simulated, but where the likelihood function is *intractable*, in that it cannot be numerically evaluated in a practical time.
Examples include models of:

* Climate
* High energy physics reactions
* Variation in genetic sequences over a population
* Molecular level biological reactions
* Infectious diseases

One common reason for intractability is that there are a very large number of ways in which the observable data can be generated, and it would be necessary to sum the probability contributions of all of these.

## Bayesian inference

The majority of work in this area, including the paper I'm discussing, is focused on the Bayesian approach to inference.
Here a probability distribution must be specified on the unknown parameters, usually through a density $$\pi(\theta)$$.
This represents *prior beliefs* about the parameters before any data is observed.
The aim is to learn the *posterior beliefs* resulting from updating the prior to incorporate the observations.
Mathematically this is an application of conditional probability using Bayes theorem:
the posterior is $$\pi(\theta | y_0) = k \pi(\theta) L(\theta)$$, where $$k$$ is a constant of proportionality that is typically hard to calculate.
A central aim of Bayesian inference is to produce methods which approximate useful properties of the posterior in a reasonable time.

## Simulator based inference and ABC

[Several methods](/research/2016/01/03/LFtimeline) have been proposed for inference using simulators rather than the likelihood function, sometimes called "likelihood-free inference".
One of the most popular is [approximate Bayesian computation](https://en.wikipedia.org/wiki/Approximate_Bayesian_computation).
The simplest version of this is based on [rejection sampling](https://en.wikipedia.org/wiki/Rejection_sampling):

1. Sample $$\theta_i$$ values for $$1 \leq i \leq n$$ from $$\pi(\theta)$$.
2. Simulate datasets $$y_i$$ from the model given parameters $$\theta_i$$ for $$1 \leq i \leq n$$.
3. Accept parameters for which $$d(y_i, y_0) \leq \epsilon$$, and reject the remainder.

Here the user must specify the number of iterations $$n$$,
the acceptance threshold $$\epsilon$$,
and the distance function $$d(\cdot,\cdot)$$.

The accepted parameters are a sample from an approximation to the posterior with density proportional to
$$\int \pi(\theta | y) I(d(y,y_0) \leq \epsilon) dy$$
(where $$I$$ represents an indicator function: 1 when $$y$$ makes its argument is true and 0 otherwise.)

Obtaining a sample from the posterior is a standard approach to Bayesian inference known as the *Monte Carlo method*.
Such a sample can be used to approximate most interesting properties of the posterior.
Sampling from an approximation to the posterior, as in ABC, allows inference but adds an extra layer of approximation.

Rejection sampling is inefficient.
Typically the posterior is much more concentrated than the prior, especially if there are more than a handful of parameters to learn.
Therefore most simulations will be rejected.
Several more sophisticated ABC algorithms have been invented to avoid this difficulty.
These include versions of [Markov chain Monte Carlo](http://www.pnas.org/content/100/26/15324.short) (MCMC) and [sequential Monte Carlo](http://rsif.royalsocietypublishing.org/content/6/31/187.short) (SMC).
Both of these propose $$\theta$$ values which attempt to focus on regions of higher posterior probability.

More recently several other algorithms have been proposed to improve the efficiency of ABC.
These include ideas such as [Bayesian optimisation](http://arxiv.org/abs/1502.05503),
[learning latent variables](http://papers.nips.cc/paper/5881-optimization-monte-carlo-efficient-and-embarrassingly-parallel-likelihood-free-inference)
[expectation propagation](http://arxiv.org/abs/1512.00205)
and [classical testing theory](http://arxiv.org/abs/1305.4283),
as well as various methods which, like that of Papamakarios and Murray, use [classifiers](http://arxiv.org/abs/1506.02169) and [regression](http://arxiv.org/abs/1605.05537) methods from machine learning.

## Limitations of likelihood-free methods

ABC works well for sufficiently simple problems.
For example see this [review article](http://www.annualreviews.org/doi/abs/10.1146/annurev-ecolsys-102209-144621?journalCode=ecolsys) on applications in genetics and ecology.
But it has several serious drawbacks in more difficult problems.

### 1) Tuning requirements

The ABC rejection algorithm outlined above requires selection of a threshold $$\epsilon$$.
There's some theoretical work on its optimal choice in an asymptotic regime of a very large number of simulations, but it's hard to apply this in practice.
One reason is that more complex ABC algorithms require many other tuning choices with interact with selection of $$\epsilon$$.
A particularly challenging issue is the choice of summary statistics (see item 4 in this list).

### 2) Validation

ABC samples from an approximation to the posterior, and it's hard to know how much to trust this approximation.

### 3) Expensive simulators

Even efficient ABC algorithms usually require at least tens of thousands of simulations.
This can be impractical for expensive simulators.

### 4) High dimensional data (and parameters)
 
ABC suffers from a *curse of dimensionality* problem.
This is because it uses a nearest neighbours type approach.
Parameters are inferred based on the simulated data sets which are closest to the observations.
But as the dimension of the data increases, the simulations become increasingly sparse in the space of data sets, so the nearest neighbours become worse representations of the observed data.
This can be a problem for ABC even when the dimension of the data reaches as low as the tens!

To deal with this problem practitioners have used dimension reduction methods to replace high dimensional data with low dimensional summary statistics.
This tuning choice involves some loss of information about the parameters.
It's hard to judge which choice of summaries makes the best trade-off between information and dimension, although many methods have been proposed.

ABC usually also performs poorly for high dimensional parameters.
This is because learning such parameters generally entails having data which is high dimensional enough to cause problems.

In the [second blog post](/research/2016/06/07/bayesian-inference-by-neural-networks2) I'll look at how the new paper tackles some of these problems.
