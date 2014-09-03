---
layout: page
title: Hello World!
tagline: no offense to the rest of the universe
---

##Intro

Hi all,
This is some kind of website. Up to now it is all messy, since I have
no content, except some standard docs which where already here when I
installed this stuff. Feel free to look around.



## Posts
 Here is a list of posts.

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
