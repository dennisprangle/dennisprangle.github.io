---
layout: post
title: "Posters in LaTeX"
category: Tips
tags: [Posters, Tips, LaTeX, Newcastle university]
---
{% include JB/setup %}

LaTeX is designed for typesetting mathematical documents, but it can also be used for posters.
It's not the only option, but it's pretty convenient if you want to include a lot of mathematics in your poster and/or quickly reuse material from an article.
This post distills the advice I usually give students who are making their first poster.

There are several LaTeX packages for posters.
I use *beamerposter* which I think is the most popular.
This is great for making a professional poster quickly, but if you want to stand out try something more unusual.
I've heard some good things about alternatives including [minimal-poster](http://asishghoshal.com/blog/2017/minimal-poster/) and [tikzposter](https://ctan.org/pkg/tikzposter) but haven't tried them.
See [this stack exchange answer](https://tex.stackexchange.com/questions/341/how-to-create-posters-using-latex) for many more options.

A good place to start by editting an existing example such as [this template](http://www.latextemplates.com/template/jacobs-landscape-poster) or the one in this [blog post by Rob Hyndman](http://robjhyndman.com/hyndsight/beamer-poster/).
When creating a poster you need to select a theme describing some style choices.
The packages comes with some [defaults](https://github.com/deselaers/latex-beamerposter/tree/master/themes).
For colleagues/students at Newcastle University, [here's a theme including the university logo](https://gist.github.com/dennisprangle/788783f6a7679f4dde7373b6dc21cb9e).
(created by editting a theme passed down to me by a former colleague.)
You'll need to download the logo separately.
The exact file I used is at available [here](http://www.nodes.ac.uk/wp-content/themes/hotrod/img/logo/university/Newcastle_University.png) for example.
(This logo is probably a bit out of date now.
There are some [more recent files](https://wiki.mas.ncl.ac.uk/mas/SchoolGraphics) on the NU maths wiki, if you have access to that.
But you may need to play around with image sizes etc. to make it look nice.)

The `.sty` file used to define a beamerposter theme is fairly easy to edit, and lets you tweak a lot of the appearance options.
An undergraduate dissertation student a couple of years ago improved on my theme file a lot like this but sadly I never got hold of his code!

For more on beamerposter, here's [a journal article on using it](http://tug.org/pracjourn/2012-1/shang/shang.pdf), its [Comprehensive TeX Archive Network page](https://ctan.org/pkg/beamerposter) and its [github page](https://github.com/deselaers/latex-beamerposter).
Finally, here are some tips on how you can [recycle your used fabric poster into clothing](https://veronikach.com/lifestyle/how-to-recycle-your-fabric-poster-faq/).
