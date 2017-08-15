---
layout: post
category : lessons
tagline: "Supporting tagline"
tags : [intro, beginner, jekyll, tutorial]
---
{% include JB/setup %}

I've recently been doing some work on **hidden Markov models** (HMMs).
Since these have been a gap in my stats knowledge, I've had to do some background reading on their theory, and I thought I'd write up a summary as a blog post.

This presentation of the material is based on ["Inference in Hidden Markov Models"](http://www.springer.com/gp/book/9780387402642)
by Olivier Cappé, Eric Moulines and Tobias Ryden,
which is a comprehensive technical reference,
and [the wikipedia page for the forward-backward algorithm](https://en.wikipedia.org/wiki/Forward%E2%80%93backward_algorithm),
which is more basic but very accessible.

# Notation

I'm going to use the common notation $y_{a:b}$ as shorthand for the sequence $y_a, y_{a+1}, y_{a+2}, y_b$.

I'll represent random variables by capitals and corresponding values as lower case letters.
For example $Y_1$ is a random variable modelling potential observations at time 1
and $y_1$ is the actual observed value (typically a vector rather than a number).

Matrices will also be represented by capital letters.

I'll assume that all relevant distributions of continuous quantities have probability density functions (pdfs).

Suppose $x$ is discrete and $y$ is continuous.
I'll denote a probability for $x$ as $\Pr(x)$ and a pdf for $y$ as $\pi(y)$.
There will be some joint distributions of discrete and continuous quantities below.
Abusing notation slightly these are denoted by $\pi(x,y)$.
(The more principled way to deal with this would be to do would be to use measure
theory notation throughout, which Cappé et al do. I avoid this here for simplicity.)

# Model definition

HMMs model time series data $y_{1:T}$.
Here I'll assume that each $y_t$ is a (real-valued) vector.

The model involves an unobserved (i.e. "hidden") Markov chain $X_1, X_2, \ldots, X_T$.
I'm going to focus on the case where $X_t$ can take a finite set of values.
The value of $X_t$ is the "**state**" at time $t$.

I'll assume the possible values of the $X$s are the integers $1,2,\ldots,k$.
So $k$ is the number of possible states.

The Markov chain is defined by:

1. A vector $q$ of probabilities for its initial state i.e. $q(i) = \Pr(X_1=i)$.
2. A **transition matrix** $M$ whose $ij$th entry is $m(i,j) = \Pr(X_{t+1}=j | X_t=i)$.

The observation variable $Y_t$ is drawn from an **emission distribution**
depending on $X_t$.
I'll assume this is a continuous distribution, but the discrete case is similar
(Change densities to probabilities and integrals to sums.
Or generalise to measure theoretic notation.)
I'm going to consider the general case where an observation can also depend on the previous observations.

To summarise, the observation model is defined by:

* Emission pdfs $g_t(y_t|x_t,y_{1:t-1})$.

The model implicitly assumes that $Y_t$ is conditionally independent of $X_i$ for $i \neq t$ and $Y_{t+1:T}$.

Two simple specific cases are:

###### Gaussian HMM  
$$
Y_t | X_t = i \sim N(\mu_i, \Sigma_i)
$$

###### VAR(1)-HMM

Var(1) is short for vector autoregressive model of order 1.
Here
$$
Y_t | X_t=i, Y_{t-1}=y_{t-1} \sim N(\mu_i + A_i y_{t-1}, \Sigma_i)
$$  
(A model for $Y_1 | X_1$ must also be supplied.)

# Parameter Inference

I'm particularly interested in the parameter inference problem.
That is, given observations $y_{1:T}$, I want to learn the parameters:

* The initial distribution $q$.
* The transition matrix $M$.
* The emission distribution parameters.  
  * $\mu_{1:k}$ and $\Sigma_{1:k}$ in the Gaussian HMM case, or
  * $A_{1:k}$, $\mu_{1:k}$ and $\Sigma_{1:k}$ in the VAR(1)-HMM case

Note this doesn't include $k$ which I'll assume is specified in advance.
I will write $\theta$ to represent all the parameters to be learnt.

I'm going to look at maximum likelihood methods for learning the parameters
based on [gradient descent](https://en.wikipedia.org/wiki/Stochastic_gradient_descent).
This is because I have a large dataset to analyse which makes this method particularly appropriate.
In particular, we can attempt to do efficient inference for large $T$ by using gradients based on shorter intervals of data, although this involves some approximations.
I won't go into the details of this here (maybe a future blog post?)

There are many alternative inference methods.
The standard maximum likelihood method is to use the [EM-algorithm](https://en.wikipedia.org/wiki/Expectation%E2%80%93maximization_algorithm).
All maximum likelihood methods can include regularisation terms, for example to encourage sparsity.
Bayesian approaches using MCMC are also possible but struggle with big datasets.
There's some interesting recent literature on scaling up Bayesian methods for HMMs to this case
([Foti et al](http://papers.nips.cc/paper/5560-stochastic-variational-inference-for-hidden-markov-models), [Ma et al](https://arxiv.org/abs/1706.04632)).

All these inference methods are based on the **likelihood function** -
the pdf of the observations given the parameters:
$$
\begin{align*}
L(\theta) &= \pi(y_{1:T} | \theta) \\
&= \sum_{x_{1:T}} \pi(x_{1:T}, y_{1:T} | \theta)
\end{align*}
$$
The second line says the likelihood is the marginal of $\pi(x_{1:T}, y_{1:T} | \theta)$ -- the "complete data likelihood".
This is the joint density of the observations and hidden states given the parameters
(as mentioned earlier I'm abusing notation a bit here as the hidden states are discrete and don't admit a density unless we introduce some measure theory.)
We can write a simple expression for the complete likelihood:
$$
\pi(x_{1:T}, y_{1:T} | \theta)
= q(x_1) \prod_{t=1}^{T-1} m(x_t, x_{t+1}) \prod_{t=1}^T g_t(y_t|x_t, y_{1:t-1})
$$
The difficulty is it's not obvious how to marginalise this neatly.

To apply gradient descent we want to find the gradient of $\ell(\theta) = \log L(\theta)$.
Using [Fisher's identity](http://www.almoststochastic.com/2014/06/fishers-identity.html),
this is:
$$
\nabla \ell(\theta) = E_{X_{1:T} | y_{1:T}, \theta}[ \nabla \log \pi(x_{1:T}, y_{1:T} | \theta)]
$$
That is, we need to find the gradient of the complete data log-likelihood,
and then take the expectation with respect to $X_{1:T}|y_{1:T},\theta$ --
the posterior distribution of the states given the observations and parameters.
This gives
$$
E_{X_1|y_{1:t}, \theta}[\nabla \log q(X_1)]
+ \sum_{t=1}^{T-1} E_{X_t, X_{t+1} | y_{1:t}, \theta}[\nabla \log m(X_t, X_{t+1})]
+ \sum_{t=1}^T E_{X_t | y_{1:t}, \theta}[\nabla \log g_t(y_t|X_t, y_{1:t-1})]
$$
So we can get an expression for $\nabla \ell(\theta)$ if we can work output
marginal and bivariate posteriors of the states.
This can be done by the **forward-backward algorithm** described in the following sections.

# Forward-backward algorithm

I'll present the forward-backward algorithm in this section as a method to derive marginal state probabilities $\Pr(X_t=i|y_{1:T},\theta)$.
Some other useful quantities are computed along the way.
It's also possible to extend the algorithm to compute bivariate state probabilities
$\Pr(X_t=i, X_{t+1}=j|y_{1:T},\theta)$.
This will be covered in the next section.

## Forward loop

The forward loop calculates the probabilities
$\hat{\alpha}_t(i) = \Pr(X_t = i | y_{1:t}, \theta)$.
Let $\hat{\alpha}_t$ denote the vector of $\hat{\alpha}_t(i)$ values.

An algorithm to compute these is as follows:

Loop over $t$ in $1:T$:
1. Calculate the vector $g_t$ of observation densities $g_t(i) = g_t(y_t | i, y_{1:t-1})$.
2. If $t=1$, let $\alpha_t = g_1 \odot q$ (where $q$ is the vector of starting probabilities and $\odot$ is elementwise multplication).  
Otherwise let $\alpha_t = (g_t \odot \hat{\alpha}_t)' M$ (where $M$ is the transition probability matrix and a dash represents transpose)
3. Let $c_t = \sum_i \alpha_t(i)$.
4. Let $\hat{\alpha}_t(i) = \alpha_t / c_t$.

Using Bayes theorem it can be seen that the first iteration calculates:
$$
\begin{align*}
\alpha_1(i) &= \pi(x_1=i, y_1 | \theta)  \\
c_1 &= \pi(y_1 | \theta) \\
\hat{\alpha}_1(i) &= \pi(x_1=i | y_1, \theta),
\end{align*}
$$
and each subsequent iteration calculates:
$$
\begin{align*}
\alpha_t(i) &= \pi(x_t=i, y_t | y_{1:t-1}, \theta) \\
c_t &= \pi(y_t | y_{1:t-1}, \theta) \\
\hat{\alpha_t}(i) &= \pi(x_t=i | y_{1:t}, \theta).
\end{align*}
$$

## Backward loop

Define:
$$
\beta_t(i) = \pi(y_{t+1:T} | X_t=i, y_{1:t}, \theta).
$$
The backward loop calculates quantities:
$$
\hat{\beta}_t(i) = \frac{\beta_t(i)}{\prod_{s=t+1}^t c_s}.
$$

An algorithm to compute these is as follows:

* Let $\hat{\beta}_T$ be a vector of 1s.  
* Loop over $t=T-1,T-2,\ldots,1$:
    * Let $\hat{\beta}_t = M (\hat{\beta}_{t+1} \odot g_{t+1}) / c_{t+1}$.

Equivalently $\beta_T$ is a vector of 1s,
and $\beta_t = T (\beta_t \odot g_t)$.

Again it can be checked using Bayes theorem that this recursion gives the desired quantity.

## Marginal probabilities

We want:

$$
\begin{align*}
\Pr(X_t=i|y_{1:T},\theta) &=
\frac{\Pr(X_t=i|y_{1:t},\theta) \pi(y_{t+1:T} | X_t=i, y_{1:t}, \theta)}
{\pi(y_{t+1:T}|y_{1:t}, \theta)} \\
&= \frac{\hat{\alpha}_t(i) \beta_t(i)}{\prod_{s=t+1}^t c_s} \\
&= \hat{\alpha}_t(i) \hat{\beta}_t(i).
\end{align*}
$$

So the marginal probabilities are found simply by multiplying together the outputs of the forward and backward loops.

# Bivariate probabilities

It's also possible to infer bivariate distributions over the HMM states from the forward-backward algorithm output. The result is given in Cappé et al, and here is a more detailed derivation.

We want:
\[
\Pr(X_t = i, X_{t+1} = j | y_{1:T}, \theta),
\]
To save space I'll omit the conditioning on $\theta$ from now on.
The desired probability can be decomposed as:
\[
\Pr(X_t = i | y_{1:T})
\Pr(X_{t+1} = j | X_t = i, y_{1:T}).
\]
The first of these factors is a univariate marginal, which we already have.
Next we use Bayes theorem to write the second factor as:
\[
\frac{\pi(y_{1:T} | X_t = i, X_{t+1} = j) \Pr(X_{t+1} = j | X_t = i)}
{\pi(y_{1:T} | X_t = i)}.
\]
The second factor in the numerator is the state transition probability $m(i,j)$.
It remains to calculate the denominator and other numerator factor.
Using conditional independence we have:
\[
\begin{align*}
\pi(y_{1:T} | X_t = i, X_{t+1} = j) =&
\pi(y_{1:t-1} | X_t = i)
\pi(y_t | X_t = i, y_{1:t-1}) \\
& \pi(y_{t+1} | X_{t+1} = j, y_{1:t})
\pi(y_{t+2:T} | X_{t+1} = j, y_{1:t+1}),
\end{align*}
\]
and
\[
\pi(y_{1:T} | X_t = i) = \pi(y_{1:t-1} | X_t = i)
\pi(y_t | X_t = i, y_{1:t-1})
\pi(y_{t+1:T} | X_t = i, y_{1:t}).
\]
Taking the ratio of these expressions gives:
$$
\begin{align}
\frac{\pi(y_{1:T} | X_t = i, X_{t+1} = j)}{\pi(y_{1:T} | X_t = i)}
&= \frac{\pi(y_{t+1} | X_{t+1} = j, y_{1:t}) \pi(y_{t+2:T} | X_{t+1} = j, y_{1:t+1})}
{\pi(y_{t+1:T} | X_t = i, y_{1:t})} \\
&= \frac{g_{t+1}(y_{t+1} | j, y_{1:t}) \beta_{t+1}(j)}
{\beta_t(i)}.
\end{align}
$$
Recall that:
\[
\beta_t(x) = \hat{\beta}_t(x) \prod_{s=t+1}^T c_s.
\]
So:
\[
\frac{\beta_{t+1}(j)}{\beta_t(i)} =
\frac{\hat{\beta}_{t+1}(j)}{c_{t+1} \hat{\beta}_t(i)}.
\]
Combining all these results gives:
\[
\Pr(X_t = i, X_{t+1} = j | y_{1:T})
= \frac{\Pr(X_t = i | y_{1:T}) m(i,j) g_{t+1}(y_{t+1} | j, y_{1:t}) \hat{\beta}_{t+1}(j)}{c_{t+1} \hat{\beta}_t(x)}.
\]
(In my 2005 edition of Cappé et al, this corresponds to the equation at the bottom of page 75.
However I think their statement has a typo -- the $c$ term should be $c^{-1}$.)
