---
layout: page
title: Dennis Prangle's home page
---
{% include JB/setup %}

I'm transferring my personal website here.
Here is the content so far...

- [Publications](pub.html)
- [My CV](https://dl.dropboxusercontent.com/u/5302937/DennisPrangleCV.pdf)

## Posts list

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

