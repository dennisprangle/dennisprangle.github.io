---
layout: post
title: "Bayesian inference by neural networks. Part 2: new paper"
description: ""
category: research
tags: [ABC, Likelihood-free, Neural networks]
---
{% include JB/setup %}

In [part 1](/research/2016/06/07/bayesian-inference-by-neural-networks) I reviewed likelihood-free inference and its limitations.
This post looks at a [new approach](http://arxiv.org/pdf/1605.06376.pdf) by George Papamakarios and Iain Murray which avoids some of these issues and delivers an orders-of-magnitude improvement in performance.
Most of the post will describe their paper.
Then I'll close with a few comments on the method's advantages and possible limitations.

## Regression approaches to likelihood-free inference

For now I'll consider the problem of approximating the posterior $$\pi(\theta \vert y_0)$$ given simulations $$(\theta_i, y_i)$$ from $$\pi(\theta) \pi(y \vert \theta)$$ for $$1 \leq i \leq N$$.
The paper also simulates from other distributions, which I'll discuss below later.
(n.b. Out of habit, I'm using slightly different notation to the paper. They use $$x$$ for datasets, $$x_o$$ for the observations, and $$p$$ instead of $$\pi$$.)

As described in [part 1](/research/2016/06/07/bayesian-inference-by-neural-networks), ABC approaches operate by a nearest neighbours approach.
That is, it approximates the posterior using $$\theta_i$$ values such that $$y_i$$ is close to $$y_0$$.
But this performs poorly unless $$y$$ is low dimensional.

An alternative approach is to use *regression*.
This uses the simulations to train a system predicting $$\theta$$ from $$y$$ and then returns the predictions for $$y_0$$.
Rather than just base its output on the nearest neighbours, regression uses *all* the data to learn relationships between *features* of $$y$$, $$f_1(y), f_2(y), \ldots$$, and $$\theta$$.
(The assumption that such relationships exist is, I think, what the machine learning community sometimes refers to as "compositionality".)
Hopefully even $$y_i$$ values which are not close to $$y_0$$ are useful in learning about these relationships.
[Classical linear regression](https://en.wikipedia.org/wiki/Linear_regression) requires the user to define the features and allows only linear relationships.
Modern methods such as neural networks (a) can learn the features and (b) use non-linear relationships, although allowing more complexity in either requires more data to learn well.

Most regression methods output a single "point" prediction.
This is problematic here as we want to learn more than this.
In the Bayesian setting we want to learn a whole distribution, summarising the uncertainty about $$\theta$$ given $$y_0$$.
In the next section I'll outline how Papamakarios and Murray deal with this.

There is a history of using regression in likelihood-free methods.
[Beaumont Zhang and Balding](http://www.genetics.org/content/162/4/2025.short) find nearest neighbours in a traditional ABC step, then use these for regression.
A nice feature of this approach is that the set of nearest neighbours gives a distribution for $$\theta$$, which is then adjusted by the regression.
[Blum and François](http://link.springer.com/article/10.1007/s11222-009-9116-0) generalise this to use neural network regression.
[Bonassi and West](http://www.degruyter.com/view/j/sagmb.2011.10.issue-1/1544-6115.1684/1544-6115.1684.xml) propose a regression approach (with no initial ABC step) based on fitting a mixture of normals to the $$(\theta_i,y_i)$$ simulations.

## Mixture density networks

Papamakarios and Murray use a [feed-forward neural network](https://en.wikipedia.org/wiki/Feedforward_neural_network) approach.
These are composed of several layers of variables, the initial layer being the inputs, in this case $$y$$.
The next layer is created by applying a linear transformation and then element-wise non-linear transformations.
That is, an element of the second layer is a formed by taking a weighted sum of the $$y$$s and then applying a [logistic function](https://en.wikipedia.org/wiki/Logistic_function) or something similar.
Subsequent layers are formed similarly.
The final layer is the output, which for example could be predictions of $$\theta$$.
The network is specified by all the weights it uses.
These are tuned by optimisation: ensuring the predictions are as close as possible to the true $$\theta_i$$s according to some mathematical function (e.g. minimising sum of squared errors, or maximising likelihood).
A lot of techniques have been created to allow this to be done well.
One technique used in this paper (Section 2.5) which seemed important, although I didn't entirely follow it, was to use a stochastic variational inference (SVI) method to avoid overfitting due to small sample sizes.

As mentioned above, neural network regression provides the advantages of learning features of $$y$$ - the variables in intermediate layers - and fitting non-linear relationships.
However it has the disadvantage of not outputting a distribution.
To deal with this the paper uses a *mixture density network* (MDN),
which outputs the parameters of a *mixture distribution*, in this paper a normal mixture.
This can be viewed as representing the posterior distribution as several clusters.
Each cluster has an associated weight, and is defined by its mean vector and variance matrix.
The final layer of neural network contains all of these details, represented in a neat form that ensures any output gives a valid distribution.
The network is fitted by maximising the density of the observed $$\theta$$ values under the mixture density, which is performed iteratively on small batches of data using an efficient variant on stochastic gradient descent.

One reason to concentrate on mixtures of normals is that it is straightforward to calculate many posterior summaries without the need to resort to Monte Carlo.
They also facilitate the sequential methods described below.

A nice feature of the paper is that the neural networks are not deep.
They use only one or two hidden layers and are fitted to a relatively small amount of simulated data.
Only up to around $$10^5$$ simulated datasets are used for training, and much fewer for the most efficient of the proposed methods.
(Although one example does also require a pilot of $$10^5$$ simulations.)

## Sequential simulation

The set-up above tries to learn the global relationship between the posterior $$\pi(\theta \vert y)$$ and $$y$$, which is potentially very complicated.
The paper proposes focusing on a local part of this relationship which is relevant to learning $$\pi(\theta \vert y_0)$$, with the goal of reducing the amount of simulated data required.

Papamakarios and Murray do this by using a sequential approach.
First they learn a rough estimate of the posterior $$\tilde{\pi}(\theta)$$ using the MDN approach outlined above.
As will be seen shortly, it turns out to be useful to just fit a normal distribution rather than a mixture.
Then they sample $$(\theta_i, y_i)$$ pairs from the distribution $$\tilde{\pi}(\theta) \pi(y|\theta)$$.
Using a MDN the corresponding mixture estimate $$q(\theta | y_0)$$ is learnt.
This is adjusted by importance sampling to take into account the sampling distribution, to produce $$\frac{\pi(\theta)}{\tilde{\pi}(\theta)} q(\theta | y_0)$$.
The paper focuses on the case where $$\pi(\theta)$$ is normal or uniform.
In this case, since $$\tilde{\pi}(\theta)$$ is also normal, the resulting posterior estimate can be shown to itself be a mixture of normals (see Appendix C of the paper).

The paper considers several schemes:

1. A non-sequential approach fitting a mixture estimate.
2. Several iterations of sequential scheme above, always returing a normal estimate of the posterior. (This was where the SVI method mentioned above was needed.)
3. Using the result of 2 as a proposal density to fit a mixture estimate of the posterior.

As noted in the paper, this general idea of a sequential approach is similar in spirit to existing sequential Monte Carlo algorithms for ABC.
Edit: In this literature, it's considered to be a good idea to sample $$\theta$$ from a wider distribution than the current approximation of the posterior:
see [Beaumont et al](http://biomet.oxfordjournals.org/content/96/4/983) and a more theoretical argument in [Li and Fearnhead](http://arxiv.org/abs/1506.03481).
I wonder if the same applies here.

## Simulation studies

The paper contains several simulation studies, studying how well the method learns for observations simulated under known parameter values.
In all studies the MDN methods substantially outperform ABC methods: rejection, MCMC and SMC.
Typically fitting the *entire posterior* has a cost is comparable to producing a *single* effective sample under ABC.
And the quality of the posterior fits is comparable to or better than the best produced under ABC, without the need to worry about what value of $$\epsilon$$ should be used.

Of the 3 MDN schemes mentioned above, the sequential methods require substantially fewer parameters.
In some cases the quality of the fit is similar for both of these methods, but in others the mixture model is superior to the normal model.

## Comments

This paper certainly seems like a big step forwards for likelihood-free inference, making substantial progress on most of the limitations listed in my [first post](/research/2016/06/07/bayesian-inference-by-neural-networks).
Benefits include:

* Beating the performance of ABC methods by orders of magnitude.
* Avoiding the tuning parameter $$\epsilon$$, which is notoriously hard to select and interpret.
* Reducing - but not eliminating, see below - the need to choose summary statistics.

However as an academic I'm required to be cautious and say further research is needed to validate the performance of the algorithm in applied settings.
In particular one issue with existing ABC regression methods is that they can produce very poor estimates when the observations are substantially different to the simulated data.
This is because the regressions need to extrapolate rather than interpolate.
I wonder if the MDN approach is robust to this sort of problem.

Finally here are some other comments on the approach.

* **Tuning choices** Is there a good way to decide tuning issues such as how many mixture components to use and the architecture the neural network?

* **Scaling to high dimensional data** The paper considers two examples with high dimensional data, a model of population dynamics and a queuing system. The population example has around 300 observations (large in the context of likelihood-free methods!) The queuing example has a variable number of observations. In both case a small number of summary statistics of the data are used as input to the neural network. This raises several questions (a) what's a good way to choose summary statistics here - are methods proposed for ABC still of any use? (b) can we judge how much information is lost by using summary statistics (c) can dimension reduction be included as an initial stage of the neural network e.g. through some suitable sparse network structure to avoid needing an enormous amount of data.

* **Parameterisation** Some posteriors are closer to normal after a parameter transformation. Perhaps a suitable transformation - e.g. elementwise Box Cox transformations - could be learnt jointly with the rest of the model.

* **Uncertainty** It would be nice to take into account the uncertainty of the estimated posterior. Perhaps the Bayesian SVI method mentioned in the paper could produce this as a by-product.

* **Validation** Is it any easier to validate the quality of a fitted posterior than for ABC output?

* **Scaling to high dimensional parameters** The variance matrices in the mixture model requires $$O(p^2)$$ parameters (where $$p$$ is the number of parameters) which might make scaling to large $$p$$ difficult.

* **Latent variables** Sometimes we'd like to include latent variables as parameters, and then integrate them out.
For example in model choice it's generally efficient to learn about the parameters of each model.
Similarly in parameter inference perhaps it would be beneficial to learn about latent variables representing part of the stochastic simulation process.

* Edit: **Tails** Normal mixtures won't be able to match especially heavy or light tailed posteriors. Is there a flexible alternative?