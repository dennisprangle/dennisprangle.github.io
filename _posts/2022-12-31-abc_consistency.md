---
layout: post
title: "Revisiting ABC posterior convergence"
description: ""
category: research
tags: [Bayesian statistics, ABC]
---

The material in this blog post is planned to eventually be an appendix of a paper,
but I thought I'd post it here separately as it's hopefully of some independent interest.
Also, this draft is a good way to check for errors - let me know if you spot any!

The idea is to prove convergence of the ABC posterior to the true posterior as $$\epsilon \to 0$$.
I'll cover previous proofs, extend them, and relate them to the key underlying mathematical results.

## Bayesian setting

Consider the Bayesian setting with:

* Prior density $$p(\theta)$$
* Model density $$f(y \vert \theta)$$
* Observations $$y_0$$

I'll assume $$\theta \in \mathbb{R}^d$$, $$y \in \mathbb{R}^n$$,
and that $$p(\cdot)$$ and $$f(\cdot | \theta)$$ are densities with respect to Lebesgue measure.
Therefore $$f(\cdot | \theta)$$ is an integrable function.

Now let:

$$
\begin{aligned}
L(\theta) &= f(y_0 | \theta) && \text{likelihood} \\
\tilde{p}(\theta | y_0) &= p(\theta) L(\theta) && \text{unnormalised posterior density} \\
Z &= \int \tilde{p}(\theta | y_0) d\theta && \text{normalising constant} \\
p(\theta | y) &= \tilde{p}(\theta | y_0) / Z && \text{posterior density}
\end{aligned}
$$

Throughout, all integrals are over the full $$\theta$$ or $$y$$ space,
unless the domain of integration is specified.

I'll assume:

* A1: $$Z>0$$ (the observations are supported under the prior and model)

Later I'll also use the notation:

$$
Z(y) = \int p(\theta) f(y | \theta) d\theta,
$$

noting that $$Z(y_0) = Z$$ and $$\int Z(y) dy = 1$$.

**Remark:**
I think all the results below also hold for discrete $$\theta$$ and $$y$$ distributions.
I think it's also fine for $$p(\cdot)$$ to be a density with respect to a more general measure
but I'm not whether this is the case for $$f(\cdot|\theta)$$.
There are some comments below on where the proof needs modification for these cases.

## ABC posterior and likelihood

Many ABC algorithms produce samples from the **ABC posterior** $$p_\epsilon(\theta | y)$$.
For an overview see for example [Sisson et al (2018)](https://arxiv.org/abs/1802.09720).
The ABC posterior can be defined as follows:

$$
\begin{aligned}
L_\epsilon(\theta) &= \int K_\epsilon(y-y_0) f(y | \theta) dy && \text{ABC likelihood} \\
\tilde{p}_\epsilon(\theta | y_0) &= p(\theta) L_\epsilon(\theta) && \text{unnormalised ABC posterior density} \\
Z_\epsilon &= \int \tilde{p}_\epsilon(\theta | y_0) d\theta && \text{ABC normalising constant} \\
p_\epsilon(\theta | y_0) &= \tilde{p}_\epsilon(\theta | y_0) / Z_\epsilon && \text{ABC posterior density}
\end{aligned}
$$

Here $$K_\epsilon$$ is a **kernel function** and $$\epsilon > 0$$ is a **bandwidth**.
We'll consider kernels of the form:
$$
K_\epsilon(y) = \epsilon^{-n} K(y/\epsilon),
$$
where $$K$$ is **base kernel**, a non-negative function $$\mathbb{R}^n \to \mathbb{R}$$.
We assume:

* K1: $$K$$ is bounded
* K2: $$\int K(y) dy = 1$$.
* K3: $$K(y) > 0$$ for $$\vert \vert y \vert \vert \leq 1$$ (where $$\vert \vert \cdot \vert \vert$$ is the Euclidean norm).

The most common base kernel used in ABC is the **uniform kernel**:

$$
K_U(y) = 1[||y|| \leq 1] / k_U
$$

were $$1[\cdot]$$ is an indicator function
and $$k_U$$ is a suitable normalising constant
([volume of an $$n$$-ball](https://en.wikipedia.org/wiki/Volume_of_an_n-ball)).
Also common is the **Gaussian kernel**:

$$
K_G(y) = \exp[-\tfrac{1}{2} ||y||^2] / k_G
$$

where $$k_N = (2 \pi)^{n/2}$$.

**Remarks:**

* The ABC likelihood and posterior are similar to kernel density estimates, which is where the bandwidth terminology is taken from.
* The $$k_U$$ and $$k_G$$ constants ensure that K2 is satisfied.
In a lot of ABC literature this plays no role, so the kernels are often defined without these constants.
* Under K2, $$K$$ and $$K_\epsilon$$ are probability density functions.
The ABC posterior is then the posterior under the model plus independent noise from $$K_\epsilon$$ (see [Wilkinson 2013](https://doi.org/10.1515/sagmb-2013-0010)).
* In other contexts, the Gaussian kernel is known as the heat kernel.

## ABC posterior convergence

The goal is to show that the ABC posterior converges in distribution to the posterior
in the limit $$\epsilon \to 0$$.

## Previous work

[Rubio and Johansen (2013)](https://doi.org/10.1214/13-EJS819) (Proposition 1) prove ABC posterior convergence given:

* A kernel (defined slightly differently to above) with bounded support.
* Some continuity and local boundedness requirements on $$f(y \vert \theta)$$.

[Prangle (2017)](https://doi.org/10.1214/16-BA1002) (Theorem 1, in the supplement)
proves ABC posterior convergence for almost all $$y_0$$ given:

* A uniform kernel.
* A general choice of $$f(y \vert \theta)$$.

The latter proof is based on the [Lebesgue differentiation theorem](https://en.wikipedia.org/wiki/Lebesgue_differentiation_theorem) (LDT), discussed below.

The aim of this post is to generalise the result to a general likelihood and kernel satisfying K1-K3.

The two papers above are of most relevance to this post,
but other related work on ABC convergence results includes the following:

* [Blum (2010)](https://doi.org/10.1198/jasa.2010.tm09448),
[Fearnhead and Prangle (2012)](https://doi.org/10.1111/j.1467-9868.2011.01010.x)
and [Biau (2015)](http://www.numdam.org/item/AIHPB_2015__51_1_376_0/) derive results on the accuracy of estimates from ABC algorithms. These involve dealing with the error in the ABC posterior, as well as other sources of error.

* [Barber et al (2015)](http://dx.doi.org/10.1214/15-EJS988)
prove the convergence of ABC posterior expectations for a uniform kernel.

* [Bernton et al (2019)]( https://doi.org/10.1111/rssb.12312) (Proposition 3.1) prove ABC posterior convergence
following the approach of Rubio and Johansen, but under different conditions relevant to their purposes.
Amongst other authors, they also consider a different asymptotic regime: $$n \to \infty$$ (large number of observations).

## Approximations of the identity

The key tool in the proof is the following result.
I'm using the definitions and statement from
Stein and Shakarchi "Real analysis: measure theory, integration, and Hilbert spaces" (2005).

For an integrable function $$g:\mathbb{R}^n \to \mathbb{R}$$ (such as any probability density function),
consider a convolution:

$$
(g * K_\epsilon) (y_0) := \int K_\epsilon(y-y_0) g(y) dy
$$

Stein and Shakarchi, Theorem 2.1 of Chapter 3, states that

$$
\lim_{\epsilon \to 0} (g * K_\epsilon) (y_0) = g(y_0)
$$

for almost all $$y_0$$ if $$K_\epsilon$$ is an **approximation of the identity** (AOTI).
Conditions K1-K3 are sufficient for $$K_\epsilon$$ to be a AOTI.

**Remarks:**

* For small $$\epsilon$$, the convolution operation approximates the identity operation.
This is what the AOTI name refers to.
* I use AOTI to refer to both the theorem, and the class of kernels for which it holds.
Hopefully the distinction is clear from the context.
* The LDT can be viewed as the special case of AOTI when $$K$$ is the uniform kernel.
Typically the LDT is proved first, and used to derive AOTI.
* The analogous result to AOTI for discrete $$y$$ is trivial.
Therefore the main ABC convergence proof holds for discrete $$y$$ with little modification.
However I'm not sure what conditions are needed if $$f(\cdot|\theta)$$ is a density with respect to a more general measure.
* AOTI kernels are possible which do not take the form
$$K_\epsilon(y) = \epsilon^{-n} K(y/\epsilon)$$.
One application of this is to adaptive ABC distance functions (as in [Prangle 2017](https://doi.org/10.1214/16-BA1002)).
* The full theorem statement in Stein and Shakarchi has more details on the definition of an AOTI kernel,
and which $$y_0$$ points the theorem holds for (the [Lebesgue points](https://en.wikipedia.org/wiki/Lebesgue_point) of $$g$$).

## ABC convergence proof part 1: pointwise convergence of the ABC likelihood

Applying AOTI gives that for almost all $$y_0$$:

$$
\begin{aligned}
\lim_{\epsilon \to 0} L_\epsilon(\theta)
&= \lim_{\epsilon \to 0} \int K_\epsilon(y-y_0) f(y | \theta) dy \\
&= f(y_0 | \theta) \\
&= L(\theta).
\end{aligned}
$$

**Remark:**
Rubio and Johansen give an elementary proof of this result for all $$y_0$$
in the case of continuous $$f(\cdot \vert \theta)$$, and some restrictions on $$K_\epsilon$$.
AOTI is needed for non-continuous $$f$$.

## ABC convergence proof part 2: convergence of the ABC normalising constant

We have:

$$
\begin{aligned}
Z_\epsilon &= \int p(\theta) \bigg[ \int K_\epsilon(y-y_0) f(y | \theta) dy \bigg] d\theta \\
&= \int K_\epsilon(y-y_0) \bigg[ \int p(\theta) f(y | \theta) d\theta \bigg] dy && \text{by Tonelli's theorem} \\
&= \int K_\epsilon(y-y_0) Z(y) dy.
\end{aligned}
$$

So applying AOTI gives $$\lim_{\epsilon \to 0} Z_\epsilon = Z(y_0) = Z$$ for almost all $$y_0$$.

**Remarks:**

* Rubio and Johansen give a proof using the dominated convergence theorem,
which requires boundedness conditions on $$f$$.
* This proof holds when $$p(\theta)$$ is a density with respect to counting or more general measure.

## ABC convergence proof part 3: conclusion

It remains to observe that for almost all $$y_0$$:

$$
\begin{aligned}
\lim_{\epsilon \to 0} p_\epsilon(\theta | y_0)
&= \frac{p(\theta) \lim_{\epsilon \to 0} L_\epsilon(\theta)}{\lim_{\epsilon \to 0} Z_\epsilon} \\
&= \frac{p(\theta) L(\theta)}{Z} \\
&= p(\theta | y_0)
\end{aligned}
$$

Note that A1 is implicitly used above.
Finally, convergence in distribution follows from
[Scheffé's theorem](https://en.wikipedia.org/wiki/Convergence_of_random_variables#Properties).