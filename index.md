---
layout: page
title: Hello World!
tagline: no offense to the rest of the universe
---

##Intro

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
