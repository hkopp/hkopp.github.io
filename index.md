---
layout: page
title: Hello World!
tagline: no offense to the rest of the universe
---

## Intro

Hi all,
This is some kind of website. Up to now it is all messy and it will
probably remain like this.  
Feel free to look around.



## Posts
 Here is a list of posts.

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

## Other links

<a href="http://quantocracy.com">
<img src="{{ site.url }}/assets/index/quantocracy-badge-130.png" border="0">
</a>  
Quantocracy is a news aggregator of quant blogs and the way to go if
you want to learn what is currently going on in the quant scene.
