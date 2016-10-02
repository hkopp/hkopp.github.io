---
layout: page
title: Hello World!
tagline: no offense to the rest of the universe
---

## Intro

Hi all,  
Welcome to my blog where I write mainly about math, finance, programming,
and other stuff i find interesting.  
Feel free to look around.

## Posts
Here is a list of posts.

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

## Other links

- <a href="http://quantocracy.com">Quantocracy</a> is a news
  aggregator of quant blogs and the way to go if you want to learn
  what is currently going on in the quant scene.
- <a href="https://www.quantstart.com">Quantstart</a> is a
  high-quality low-quantity site about trading strategies.
- <a href="https://uss.informatik.uni-ulm.de/">Ulm Security
  Sparrows</a> are a bunch of computer security enthusiasts at my
  university.
