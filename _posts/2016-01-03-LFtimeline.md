---
layout: post
title: "Likelihood-free timeline"
description: ""
category: Research
tags: [Likelihood-free, ABC]
---
{% include JB/setup %}

During my PhD I started compiling a mental timeline of the appearance of various "likelihood-free" inference methods.
I wrote this up for a talk a couple of years ago and I've finally got round to making an online version so I can continue to add to it.
Please let me know if there are any mistakes or things I should add.

The methods listed perform statistical inference based on repeated model simulations rather than likelihood evaluations, which are expensive or impossible for some complex models.
There are some other ways to avoid likelihood evaluations - e.g. [empirical likelihood](http://www.pnas.org/content/110/4/1321.short) - which could also be thought as a "likelihood-free", so perhaps there's a need for a more precise but equally catchy name in the future!

### 1980s

- 1984 [Diggle and Gratton](http://www.jstor.org/stable/2345504) on inference for implicit models. This paper also discusses some precursors in the 1970s which use ad-hoc likelihood-free methods for particular applications.

- 1984 [Rubin](http://projecteuclid.org/euclid.aos/1176346785) presents a likelihood-free rejection sampling algorithm as an intuitive explanation of Bayesian methods, but not as a practical method.

- 1989 **Simulated method of moments**, [McFadden](http://www.jstor.org/stable/1913621) (econometrics).

### 1990s

- 1992 **GLUE**, [Beven and Binley](http://onlinelibrary.wiley.com/doi/10.1002/hyp.3360060305/abstract) (hydrology).

- 1993 **Indirect inference**, [Gourieroux et al](http://onlinelibrary.wiley.com/doi/10.1002/jae.3950080507/abstract) (econometrics).

- 1997 Approximate Bayesian computation (**ABC**) (population genetics). Early papers include [Tavare et al](http://www.genetics.org/content/145/2/505.short) and [Fu and Li](http://mbe.oxfordjournals.org/content/14/2/195.short).

### 2000s

- 2006 **Convolution filter**, [Campillo and Rossi](http://ieeexplore.ieee.org/xpls/abs_all.jsp?arnumber=4177291).

- 2006 **Iterated filtering**, [Ionides et al](http://www.pnas.org/content/103/49/18438.short).

### 2010s

- 2010 **Synthetic likelihood**, [Wood](http://www.nature.com/nature/journal/v466/n7310/abs/nature09319.html).

- 2015 Using random forests for likelihood-free model choice, [Pudlo et al](http://bioinformatics.oxfordjournals.org/content/early/2015/12/23/bioinformatics.btv684.abstract).

- 2015 **Optimisation Monte Carlo**, [Meeds and Welling](http://papers.nips.cc/paper/5881-optimization-monte-carlo-efficient-and-embarrassingly-parallel-likelihood-free-inference) and the closely related **reverse sampler** of [Forneron and Ng](http://arxiv.org/abs/1506.04017).
