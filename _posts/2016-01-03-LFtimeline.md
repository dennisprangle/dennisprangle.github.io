---
layout: post
title: "Likelihood-free timeline"
description: ""
category: Research
tags: [Likelihood-free, ABC]
---
{% include JB/setup %}

This is an incomplete of the appearance of various "likelihood-free" inference methods.
Please let me know if there are any mistakes or things I should add.

The methods listed perform statistical inference based on repeated model simulations rather than likelihood evaluations, which are expensive or impossible for some complex models.
There are some other ways to avoid likelihood evaluations - e.g. [empirical likelihood](http://www.pnas.org/content/110/4/1321.short) - which could also be thought as a "likelihood-free", so perhaps there's a need for a more precise but equally catchy name in the future!

I've also avoided listing papers on selecting summary statistics for use in these methods.
See [Blum et al, 2013](http://projecteuclid.org/euclid.ss/1369147911) and [Prangle, 2015](http://arxiv.org/abs/1512.05633) for reviews.

### 1980s

- 1984 [Diggle and Gratton](http://www.jstor.org/stable/2345504) on inference for implicit models. This paper also discusses some precursors in the 1970s which use ad-hoc likelihood-free methods for particular applications.

- 1984 [Rubin](http://projecteuclid.org/euclid.aos/1176346785) presents a likelihood-free rejection sampling algorithm as an intuitive explanation of Bayesian methods, but not as a practical method.

- 1989 **Simulated method of moments**, [McFadden](http://www.jstor.org/stable/1913621) (econometrics).

### 1990s

- 1992 **GLUE**, [Beven and Binley](http://onlinelibrary.wiley.com/doi/10.1002/hyp.3360060305/abstract) (hydrology).

- 1993 **Indirect inference**, [Gourieroux et al](http://onlinelibrary.wiley.com/doi/10.1002/jae.3950080507/abstract) (econometrics).

- 1997 Approximate Bayesian computation (**ABC**) (population genetics). Early papers include [Tavare et al](http://www.genetics.org/content/145/2/505.short) and [Fu and Li](http://mbe.oxfordjournals.org/content/14/2/195.short).

### 2000s

- 2003 **ABC-MCMC**, [Marjoram et al](http://www.pnas.org/content/100/26/15324.full).

- 2006 **Convolution filter**, [Campillo and Rossi](http://ieeexplore.ieee.org/xpls/abs_all.jsp?arnumber=4177291).

- 2006 **Iterated filtering**, [Ionides et al](http://www.pnas.org/content/103/49/18438.short).

- 2007-2012 **ABC-SMC/PMC**, [Sisson et al](http://www.pnas.org/content/104/6/1760.full), [Beaumont et al](http://biomet.oxfordjournals.org/content/96/4/983), [Toni et al](http://rsif.royalsocietypublishing.org/content/6/31/187), [Del Moral, et al](http://link.springer.com/article/10.1007/s11222-011-9271-y).

### 2010s

- 2010 **Synthetic likelihood**, [Wood](http://www.nature.com/nature/journal/v466/n7310/abs/nature09319.html).

- 2010 **Coupled ABC**, [Neal](http://link.springer.com/article/10.1007/s11222-010-9216-x) (epidemiology) based on utilising latent variables.

- 2015 **Bayesian indirect likelihood**, [Drovandi et al](http://projecteuclid.org/euclid.ss/1425492441).

- 2015 **Classifier**-based approaches: the random forest method of [Pudlo et al](http://bioinformatics.oxfordjournals.org/content/early/2015/12/23/bioinformatics.btv684.abstract) and the likelihood ratio estimation method of [Cranmer et al](http://arxiv.org/abs/1506.02169). A related but more expensive approach from 2014 is by [Pham et al](http://onlinelibrary.wiley.com/doi/10.1002/sta4.56/abstract).

- 2015 **Optimisation Monte Carlo**, [Meeds and Welling](http://papers.nips.cc/paper/5881-optimization-monte-carlo-efficient-and-embarrassingly-parallel-likelihood-free-inference) and the closely related **reverse sampler** of [Forneron and Ng](http://arxiv.org/abs/1506.04017).
Both exploit a latent variable formulation.

- 2016 [Graham and Stokey](http://arxiv.org/abs/1605.07826) use **constrained Hamiltonian Monte Carlo** to perform joint updates on parameters and latent variables conditioned on observations.

- 2016 **Automatic variational ABC**, [Moreno et al](https://arxiv.org/abs/1606.08549), using latent
variable draws in the estimation of loss function gradients.

- 2016 [Papamakarios and Murray](http://arxiv.org/abs/1605.06376) learn a **mixture density network** to predict the parameter posterior from observations.
