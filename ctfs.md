---
layout: default
title: CTFs
permalink: /ctf_writeups
---

*What's a CTF? Read [this](https://dev.to/atan/what-is-ctf-and-how-to-get-started-3f04) first.*

<div class="home">

  <h1 class="page-heading">CTF Writeups</h1>

  <ul class="post-list">
    {% for post in site.categories.ctfs %}
      <li>
        <span class="post-meta">{{ post.date | date: "%b %-d, %Y" }}</span>: <a class="post-link" href="{{ post.url | relative_url }}">{{ post.title | escape }}</a>
      </li>
    {% endfor %}
  </ul>

</div>
