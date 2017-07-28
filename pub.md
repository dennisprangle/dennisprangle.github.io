---
layout: page
title: "Publications etc"
description: "List of my publications"
---
{% include JB/setup %}

Please email me for copies of anything below that you cannot access.

Papers
------

### Preprints

- [gk: An R Package for the g-and-k and generalised g-and-h distributions](http://arxiv.org/abs/1706.06889)  
Dennis Prangle

- [Recalibration: A post-processing method for approximate Bayesian computation](http://arxiv.org/abs/1704.06374)  
Guilherme Rodrigues, Dennis Prangle, Scott Sisson

- [Summary statistics in Approximate Bayesian Computation](http://arxiv.org/abs/1512.05633)  
Dennis Prangle
  - Preprint of a chapter to appear in the Handbook of ABC, edited by S. Sisson, Y. Fan, and M. Beaumont.

### 2017

- [A rare event approach to high dimensional Approximate Bayesian computation](https://link.springer.com/article/10.1007/s11222-017-9764-4)
*Statistics and Computing*  
Dennis Prangle, Richard G. Everitt, Theodore Kypraios
  - See also the [supporting code](https://github.com/dennisprangle/RareEventABC.jl).

- [Adapting the ABC distance function](http://projecteuclid.org/euclid.ba/1460641065)
*Bayesian Analysis*  
Dennis Prangle
  - See also the [code used in the paper](https://github.com/dennisprangle/ABCDistances.jl).  
![Paper image]({{ site.url }}/assets/adapting_ABC_distance.jpg "Distance adaption"){:height="300px" width="300px"}

- [A tutorial introduction to Bayesian inference for stochastic epidemic models using Approximate Bayesian Computation](http://www.sciencedirect.com/science/article/pii/S0025556416300839)
*Mathematical Biosciences*  
Theodore Kypraios, Peter Neal and Dennis Prangle
  - See also the [supporting code](https://github.com/kypraios/epiABC).

### 2016

- [An ABC interpretation of the multiple auxiliary variable method](http://arxiv.org/abs/1604.08102)  
Dennis Prangle and Richard G. Everitt
  - Technical report

- [Lazy ABC](http://link.springer.com/article/10.1007/s11222-014-9544-3) *Statistics and Computing*  
Dennis Prangle
  - See also [blog post](https://dennisprangleblog.wordpress.com/2014/06/02/lazy-abc), [example code](https://github.com/dennisprangle/lazyABCexample) and summary paper "Lazier ABC".

### 2015

- [abctools: an R package for tuning approximate Bayesian computation analyses](https://journal.r-project.org/archive/2015-2/nunes-prangle.pdf) *The R Journal*  
Matthew Nunes and Dennis Prangle

- [The identification of individuals of advanced age using degeneration of the sternal end of the clavicle](http://onlinelibrary.wiley.com/doi/10.1002/ajpa.22639/full) *American journal of physical anthropology*  
Ceri G. Falys and Dennis Prangle

### 2014

- [Lazier ABC](https://www.dropbox.com/s/b3d6d2vkgpzq8ze/lazier.pdf) Contributed NIPS workshop paper  
Dennis Prangle
  - Short 5 page paper summarising "Lazy ABC" plus some extensions.
  - This link is slightly more up to date than the arXiv version.

- [Diagnostic tools of approximate Bayesian computation using the coverage property](http://onlinelibrary.wiley.com/doi/10.1111/anzs.12087/abstract) *Australian & New Zealand Journal of Statistics*  
Dennis Prangle, Micheal Blum, Gordana Popovic, Scott Sisson
  - Methods implemented by the abctools R package (see below).

- [Semi-automatic selection of summary statistics for ABC model choice](http://www.degruyter.com/view/j/sagmb.2014.13.issue-1/sagmb-2013-0012/sagmb-2013-0012.xml)
*Statistical Applications in Genetics and Molecular Biology*  
Dennis Prangle, Paul Fearnhead, Murray P. Cox, Patrick J. Biggs, Nigel P. French

### 2013

- [A comparative review of dimension reduction methods in approximate Bayesian computation](http://projecteuclid.org/euclid.ss/1369147911)
*Statistical Science*  
Micheal Blum, Matt Nunes, Dennis Prangle, Scott Sisson

### 2012

- [Constructing summary statistics for approximate Bayesian computation: semi-automatic ABC](http://onlinelibrary.wiley.com/doi/10.1111/j.1467-9868.2011.01010.x/abstract)
*Journal of the Royal Statistical Society Series B*  
Paul Fearnhead, Dennis Prangle
  - See my thesis below for supplementary material.

### 2011
- [Summary statistics and sequential methods for approximate Bayesian computation](https://www.dropbox.com/s/wbvtj518ztwvbxu/thesis_DP.pdf) *PhD thesis*  
Dennis Prangle
  - [Version without hyperlinks](https://www.dropbox.com/s/9xe2leu4nz6i8h5/thesis_DP_nohyper.pdf).  
![Thesis image]({{ site.url }}/assets/thesis.jpg "Thesis binding")

Software packages
-----------------

- abctools R package for tuning ABC analyses. Available on [CRAN](http://cran.r-project.org/web/packages/abctools) or [github](https://github.com/dennisprangle/abctools). Co-authored with Matt Nunes.

  - The package implements (1) methods to choose summary statistics (2) coverage property diagnostics.
  - See above for a 2015 R Journal paper explaining typical usage.

- gk R package for the g-and-k and generalised g-and-h distributions. Available on [CRAN](http://cran.r-project.org/web/packages/gk) or [github](http://www.github.com/dennisprangle/gk). A related paper is being written...

Talks and posters
-----------------

- [A rare event approach to high dimensional ABC](http://www.birs.ca/events/2017/5-day-workshops/17w5025/videos/watch/201702231535-Prangle.html)  
Video from BIRS workshop (2017).
- [Approximate Bayesian computation](https://www.dropbox.com/s/gyvmvrk8hg038h7/ABC_Dortmund.pdf)  
Slides from IBS tutorial, giving a general overview of ABC (2014).
- [Advances in approximate Bayesian computation for modelling biological data](https://www.dropbox.com/s/ymldljskmlhgjhu/IBC_DP.pdf)  
Slides from IBC conference, covering ABC model choice (2014).
- [Lazy ABC](http://www.newton.ac.uk/programmes/MCM/seminars/2014042414201.html)  
Video and slides from Isaac Newton Institute workshop (2014).
- [Constructing ABC summary statistics: semi-automatic ABC](http://precedings.nature.com/documents/5959)  
Slides from [ABC in London](http://www.bioinformatics.ic.ac.uk/abcil/index.html) meeting (2011).
- [Faster R code using C](https://www.dropbox.com/s/9tmr3lg2z1my71i/CinR.zip)  
Presentation material (2010)
  - Mainly of historical interest, but still a handy reference for myself!
  - Better options like [Rccp](http://www.rcpp.org/) are available now.




{% comment %}
	        <p><a href=ABC_Sydney_DP.pdf>Lazy ABC</a> - slides from ABC in Sydney workshop (2014) (less technical talk)</p>
		<p><a href=DARC_DennisPrangle.pdf>Approximate Bayesian Computation and Particle Filters</a> - slides from DARC talk at Reading (2014)</p>
  		<p><a href=seminar_DP_Leeds.pdf>Summary statistics for ABC model choice</a> - slides from Leeds seminar (2014)</p>
		<p><a href=Bayes250_DP_web.pdf>Approximate Bayesian Computation</a> - slides from Bayes 250 conference (2013)</p>
		<p><a href=STOR601_DennisPrangle.pdf>Approximate Bayesian Computation</a> - slides from a graduate lecture on ABC (2012)</p>
		<p><a href=PrOM_DP.pdf>Semi-automatic ABC</a> - slides from pre-ordinary RSS meeting on Fearnhead and Prangle (2011)</p>
{% endcomment %}
