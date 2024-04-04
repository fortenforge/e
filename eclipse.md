---
layout: default
title: The 2024 Great American Eclipse
permalink: /eclipse
---

In celebration of the 2024 Great American eclipse that crosses North America on April 8th, I'll be posting one eclipse-related post per day.

<div class="home">

  <h1 class="page-heading">Eclipse Posts</h1>

  <ul class="post-list">
    {% for post in site.categories.eclipse %}
      <li>
        <span class="post-meta">{{ post.date | date: "%b %-d, %Y" }}</span>: <a class="post-link" href="{{ post.url | relative_url }}">{{ post.title | escape }}</a>
      </li>
    {% endfor %}
  </ul>

</div>
