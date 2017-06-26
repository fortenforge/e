---
layout: default
title: Puzzlehunts
---

<div class="home">

  <h1 class="page-heading">Puzzlehunt Writeups</h1>

  <ul class="post-list">
    {% for post in site.categories.puzzlehunts %}
      <li>
        <span class="post-meta">{{ post.date | date: "%b %-d, %Y" }}</span>

        <h2>
          <a class="post-link" href="{{ post.url | relative_url }}">{{ post.title | escape }}</a>
        </h2>
      </li>
    {% endfor %}
  </ul>

  <p class="rss-subscribe">subscribe <a href="{{ "/feed.xml" | relative_url }}">via RSS</a></p>

</div>