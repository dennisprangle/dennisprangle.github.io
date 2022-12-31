---
layout: page
title: Dennis Prangle's home page
---
{% include JB/setup %}

I'm a [senior lecturer in statistics](http://www.bristolmathsresearch.org/members-of-the-institute-for-statistical-science) at the University of Bristol.

My current research is on the interface between Bayesian statistics and machine learning.
I am particularly interested in developing approximate inference methods such as [approximate Bayesian computation](https://en.wikipedia.org/wiki/Approximate_Bayesian_computation) approaches and **variational inference**.
One application is to **likelihood-free inference**, where simulation of data is possible but the likelihood function is unavailable.
Another is to **stochastic differential equations**
I've worked on applications to population genetics, physics, ecology and epidemiology.
I'm also interested in **experimental design** and how to quickly derive effective high dimensional designs.


## Resources

- [Publications](pub.html)
- [My CV](https://www.dropbox.com/s/qmyvas4mtr5fl6u/DennisPrangleCV.pdf)

## Blog posts list

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
