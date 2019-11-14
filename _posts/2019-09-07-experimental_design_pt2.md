---
layout: post
title: "High dimensional Bayesian experimental design - part II"
description: ""
category: research
tags: [Bayesian statistics, Experimental design]
---
{% include JB/setup %}

This is the second part of a blog post on a [preprint](https://arxiv.org/abs/1904.05703) by [Sophie Harbisher](https://www.ncl.ac.uk/maths-physics/postgraduate/current/pgrprofiles/harbishersophie.html),
[Colin Gillespie](https://twitter.com/csgillespie) and me.
The [first part](/research/2019/08/31/experimental_design) was about the computational benefits of using the "Fisher information gain" (trace of the Fisher information matrix)
as a utility function in Bayesian experimental design.
This part is on theoretical justification for its use.

I'm going to start with a quick intuitive explanation, by analogy with the popular **Shannon information gain** utility.
Then I'll go over a more detailed argument based on decision theory.

## Shannon information gain

One way to summarise the information provided by data $$y$$ on parameters $$\theta$$ in a Bayesian analysis
is through the **Kullback-Leibler** divergence from the prior to the posterior:

$$
\begin{equation}
D_{KL}[p(\theta | y; \tau), p(\theta)] = E_{\theta \sim p(\theta|y;\tau)} [ \log p(\theta | y; \tau) - \log p(\theta) ]. \tag{1}
\end{equation}
$$

(Recall that $$\tau$$ represents the experimental design.)

The quantity inside the expectation is known as the **Shannon information gain** (SIG):

$$
\begin{equation}
\mathcal{U}_{\text{SIG}}(\tau, \theta, y) = \log p(\theta | y; \tau) - \log p(\theta). \tag{2}
\end{equation}
$$

Maximising the expected SIG or expected KL divergence gives the same optimal design.
(Recall that the expectation is over $$\theta, y$$ from the prior and model, given $$\tau$$.)

However evaluating or estimating (2) is hard,
because it requires estimating the posterior density.
Even if posterior samples are available from Markov chain Monte Carlo then getting a density estimate is still non-trivial.

An alternative form of the SIG can be found by applying Bayes theorem:

$$
\begin{align}
p(\theta | y; \tau) &= \frac{p(\theta) f(y | \theta; \tau)}{ p(y; \tau) } \\
\Rightarrow \log p(\theta | y; \tau) &= \log p(\theta) + \log f(y | \theta; \tau) - \log p(y; \tau),
\end{align}
$$

where $$p(y; \tau) = \int p(\theta) f(y \mid \theta; \tau) d\theta$$ is the **evidence** (aka marginal likelihood or normalising constant).

Substituting this into (2) gives

$$
\begin{equation}
\mathcal{U}_{\text{SIG}}(\tau, \theta, y) = \log f(y | \theta; \tau) - \log p(y; \tau).
\end{equation}
$$

The difficulty now is estimating the log-evidence.
This is easier than estimating a posterior density but still computationally demanding.

## Fisher information gain

The approach above compares posterior and prior log densities by looking at their difference.
This is challenging because it requires correctly normalising the posterior density by computing the evidence.
An alternative is to look at the **gradient** of the difference.
As we shall see, this avoids the need to calculate the evidence.

One way to modify the KL divergence to use a gradient gives

$$
\begin{equation}
D_{FID}[p(\theta | y; \tau), p(\theta)] = E_{\theta \sim p(\theta|y;\tau)} [ || \nabla \log p(\theta | y; \tau) - \nabla \log p(\theta) ||^2 ], \tag{3}
\end{equation}
$$

where
* $$\|x\| = \sqrt{x^T x}$$ is the Euclidean norm
* $$\nabla$$ represent gradient with respect to $$\theta$$

[Stephen Walker refers to this](https://doi.org/10.1016/j.spl.2016.01.014) as the **Fisher information distance**,
and it is easy to check this is a well defined [divergence](https://en.wikipedia.org/wiki/Divergence_(statistics)) from prior to posterior.

The quantity inside the expectation in (3) is

$$
\begin{equation}
\mathcal{U}_{\text{diff}}(\tau, \theta, y) = \| \nabla \log p(\theta | y; \tau) - \nabla \log p(\theta) \|^2. \tag{4}
\end{equation}
$$

Earlier we used Bayes theorm to show that

$$
\begin{equation}
\log p(\theta | y; \tau) = \log p(\theta) + \log f(y | \theta; \tau) - \log p(y; \tau).
\end{equation}
$$

Taking the gradient gives

$$
\begin{equation}
\nabla \log p(\theta | y; \tau) = \nabla \log p(\theta) + \nabla \log f(y | \theta; \tau).
\end{equation}
$$

Note that **the evidence term vanishes** as it doesn't depend on $$\theta$$ and so has zero gradient.

Substituting this into (4) gives

$$
\begin{equation}
\mathcal{U}_{\text{diff}} = || \nabla \log f(y | \theta; \tau) ||^2,
\end{equation}
$$

and taking the expectation over $$y$$ gives

$$
\begin{equation}
\mathcal{U}_{\text{FIG}}(\tau, \theta) = E_{y \sim f(y | \theta; \tau)} [ || \nabla \log f(y | \theta; \tau) ||^2 ],
\end{equation}
$$

which turns out to equal the trace of the Fisher information.
We call this the **Fisher information gain** due to the similarity to the Shannon information gain.

## Summary so far

We started by considering the KL divergence, based on the difference in prior and posterior log densities, and showed this derived the standard SIG utility function.
We then considered an alternative divergence based on the gradient of the difference in prior and posterior log densities, and derived our FIG utility function.
The benefit of taking the gradient is that it removes the hard-to-calculate evidence term from the utility.
This allows easier optimisation of the expected FIG than the expected SIG.

The arguments so far are closely related to those in [Stephen Walker's paper](https://doi.org/10.1016/j.spl.2016.01.014).
Also the idea of looking at log density gradients to avoid normalising the posterior may seem familiar from [Hyvärinen's score matching work](http://www.jmlr.org/papers/v6/hyvarinen05a.html).
We'll see below that there is a mathematical connection.

One other thing to note is about pseudo-Bayesian utilities.
[Ryan et al](https://onlinelibrary.wiley.com/doi/abs/10.1111/insr.12107) defined a experimental design utility to be **fully Bayesian** if it's a function of the posterior and **pseudo-Bayesian** if not.
On the face of it $$\mathcal{U}_{\text{FIG}}$$ appeared to be pseudo-Bayesian.
However we can now see that it is in fact the expectation of $$\mathcal{U}_{\text{diff}}$$, which is fully Bayesian.
Therefore $$\mathcal{U}_{\text{FIG}}$$ gives the same optimal design as a fully Bayesian utility.
This suggests working with a more foundational idea of what is a justifiable utility for Bayesian experimental design.

## Bayesian decision theory and experimental design

A foundational approach to Bayesian statistics is through **decision theory**.
This was applied to experimental design by [Dennis Lindley](https://books.google.com/books?hl=en&lr=&id=As9oa7D8sAgC&oi=fnd&pg=PP1&ots=PIF8pmN-89&sig=_cpauG_QkN9yWBe6St3oeDFENQ4) amongst others.
In particular, [Jose Bernardo](https://www.jstor.org/stable/2958753) provided a decision theoretic derivation of the SIG utility.
Our work shows that a similar argument can produce the FIG utility.

Experimental design can be viewed as the following decision problem.

1. The **experimenter** selects a design, $$\tau$$.
2. **Nature** selects some parameters, $$\theta$$ (unseen by the experimenter).
3. Nature generates some data $$y$$ based on $$\tau$$ and $$\theta$$.
4. The experimenter selects an action based on $$y$$.
5. The experimenter receives a **base utility** $$\mathcal{V}$$ depending on $$\tau, \theta, y, a$$.

(We use the term "base utility" given a particular action to distinguish with "utility" $$\mathcal{U}$$ which was discussed in [part I](/research/2019/08/31/experimental_design) and will be introduced again shortly!)

As in part 1 we assume that $$\theta$$ and $$y$$ come from a prior and model.
The action could be a concrete decision - e.g. give a patient a particular treatment, pick government policy X over policy Y - in which case the base utility should ideally quantify the costs and benefits.
We're concerned with coming up with a generic approach when these details aren't available.

We'll consider the action to be making a summary of knowledge about the parameters given the data.
The action could be a point estimate for $$\theta$$, which allows a base utility such as mean squared error.
Instead we follow [Bernardo](https://www.jstor.org/stable/2958753) who suggested instead letting the action be a **distribution** for $$\theta$$.
Suitable base utilities are then supplied by the theory of [proper scoring rules](https://en.wikipedia.org/wiki/Scoring_rule), discussed below.

At step 4 the experimenter wants to pick the action maximising the expected value of $$\mathcal{V}$$ given $$\tau$$ and $$y$$.
We can define a utility function $$\mathcal{U}(\tau, y)$$ which equals the maximum expected $$\mathcal{V}$$.
We can then use the framework of [part I](/research/2019/08/31/experimental_design) and pick the design which maximises the expected $$\mathcal{U}$$.

## Scoring rules

A scoring rule $$S(q,\theta)$$ measures the quality of a prediction, here in the form of a density $$q$$, given a realised value $$\theta$$, by outputting a numerical value.
Low scores represent good matches.
Averaged over repeated predictions, the scoring rule can measure the quality of a forecaster e.g. of weather.

In our experimental design framework we can take $$\mathcal{V} = -S(a,\theta)$$ i.e. base utility equals the negative of some scoring rule.

There are many scoring rules, e.g. see [wikipedia](https://en.wikipedia.org/wiki/Scoring_rule), but these can be narrowed down by requiring a few properties

* A **proper scoring rule** has the following property. The expected score $$E_{\theta \sim p}[S(q,\theta)]$$ is minimised by $$q = p$$.
For **strictly proper** scoring rule, this is the unique global maximum.
The idea is that the optimal choice of $$q$$ (i.e. minimising expected score) is its true distribution $$p$$.
Proper scoring rules encourage forecasters to report their true beliefs.

* A **local scoring rule** $$S(q,\theta)$$ is one that depends only on $$q(\theta)$$.
So the score is only based on how likely $$\theta$$ was predicted to be, not on predictions for events that did not occur.

The logarithmic scoring rule $$S(q,\theta) = -\log q(\theta)$$ is the unique strictly proper local scoring rule (except that affine transformations are also allowed).

Other rules are possible if locality is relaxed slightly to allow $$S(q,\theta)$$ to depend on derivatives of $$q(\theta)$$.
Allowing $$k$$th derivatives is called locality of order $$k$$.
An order-2 strictly proper scoring rule is the [**Hyvärinen score**](http://www.jmlr.org/papers/v6/hyvarinen05a.html).
(I'll omit its formula as the details aren't needed here.)

The logarithmic and Hyvärinen score seem good candidates to use to create a base utility.

## Divergences from scoring rules

Given a scoring rule, various related quantities can be derived.
One is a **divergence**

$$
\begin{equation}
\mathcal{D}[p(\theta), q(\theta)] = E_{\theta \sim p}[ S(q,\theta) - S(p,\theta) ]
\end{equation}.
$$

This is the excess expected score (remember high scores are bad!) from reporting $$q(\theta)$$ rather than $$p(\theta)$$  when the true density is $$p(\theta)$$.
It turns out that logarithmic score produces the Kullback-Leibler divergence (1),
and the Hyvärinen score produces the Fisher information distance (3).

## Experimental design based on scoring rules

Suppose we take $$\mathcal{V} = -S(a,\theta)$$ in our experimental design framework.
Then in step 4 of the framework the experimenter must choose a predicted density to minimise the expected score in step 5.
If $$S$$ is strictly proper, then the best choice is the true distribution of $$\theta$$ given the available observations $$y$$.
This is the posterior $$p(\theta | y; \tau)$$.

So in step 3 the experimenter will receive a utility of the negative expected score from the posterior.
We can add a constant to the utility without affecting the decision procedure.
Adding the right constant ($$E_{\theta \sim p(\theta | y; \tau)}[p(\theta)]$$) gives a utility of

$$
\begin{equation}
\mathcal{D}[p(\theta | y; \tau), p(\theta)]
\end{equation}
$$

where $$\mathcal{D}$$ is the divergence derived from $$S$$.

This argument shows that **there is a decision theoretic justification for using a divergence derived from a scoring rule as a utility function in Bayesian experimental design**.
Using this argument, Bernardo showed that logarithmic score results in the KL divergence and SIG utility.
In our paper we show that Hyvärinen score results in the Fisher information distance divergence and FIG utility.

## Future research directions

We've shown a decision theoretic derivation of the FIG utility function in Bayesian experimental design.

But lots of questions remain for future work.
First, **what is the practical difference in the kind of designs that FIG produces compared to SIG (and other utility functions)**?
As discussed in [part I](/research/2019/08/31/experimental_design), one of our examples produced a design with lots of replicated observation times/locations.
Is FIG particularly liable to this?
In our paper we speculate that maybe FIG is too [risk seeking](https://en.wikipedia.org/wiki/Risk-seeking) - too favourable towards designs that will occasionally produce extremely informative data.

Another question is **what other utilities can be derived through decision theory**?
Perhaps we might be able to pick utilities with some desirable properties.
[Ehm and Gneiting](https://projecteuclid.org/euclid.aos/1336396185) and [Parry, Dawid and Lauritzen](https://projecteuclid.org/euclid.aos/1336396183) recently investigated the class of proper local scoring rules, and it would be interesting to see what utility functions can result from them.
Also a proper scoring rule $$S(q,\theta)$$ could be modified to $$c(\tau,y) S(q,\theta) + d(\tau,y)$$ and still be proper.
Some choices of $$c$$ and $$d$$ might provide interesting alternative utilities.
Finally perhaps we could reparameterise $$\theta$$ before applying a scoring rule in an interesting way (similarly to the weighting scheme in [part I](/research/2019/08/31/experimental_design)).