---
layout: page
title: Dennis Prangle's home page
---
{% include JB/setup %}

I'm a [lecturer in statistics](http://www.ncl.ac.uk/maths/staff/profile/dennis.prangle) at the University of Newcastle where I research methods for computational and Bayesian statistics, especially [likelihood-free](https://en.wikipedia.org/wiki/Approximate_Bayesian_computation) approaches.

## Resources

- [Publications](pub.html)
- [My CV](https://www.dropbox.com/s/qmyvas4mtr5fl6u/DennisPrangleCV.pdf)

## Blog posts list

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
