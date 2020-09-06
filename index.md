---
layout: page
title: Hello World!
tagline: no offense to the rest of the universe
---

## Intro

Hi all,  
Welcome to my blog where I write mainly about math, programming,
finance, and other stuff i find interesting.  
Feel free to look around.

### Social sites
- [Twitter](https://twitter.com/bananabunny6) for various ponderings.
- [Soundcloud](https://soundcloud.com/uftah/) if you are interested in my music.
- [Bandcamp](https://uftah.bandcamp.com/) the same music on another platform.
- [Instagram](https://www.instagram.com/uftah.music/) again mainly for music.


## Posts
My blog posts are as follows.

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

## Other links

On the internet, most private websites have been eaten by commercialized
communities. The internet I grew up with was a very different place. A
place that was more free and not as gamified, not as ad-ridden, and
not as commercialized as the current internet of fake news, lies, and
propaganda. I hate reading gamified click-baity content.
Consequently, here is a list of some of the remaining private sites.
If you own a private website that may fit into my area of interest, hit
me up and I may add you.

- [Mathematische Basteleien](http://mathematische-basteleien.de/)
  contains some mathematical curiosities. This site was one of the
  many inspirations for me to study math.
- [Ulm Security Sparrows](https://uss.informatik.uni-ulm.de/)
  are/were a bunch of computer security enthusiasts at my
  old university.

### Only German

- [c-turbines](http://www.c-turbines.ch) is the home of a crazy Swiss
  guy. Each time I visit his website I hope he has not killed himself
  yet.
