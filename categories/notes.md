---
layout: page
title: Notes
permalink: /categories/notes/
---

<ul>
{% for post in site.categories["Notes"] %}
  <li>
    <span>{{ post.date | date: "%Y-%m-%d" }}</span>
    <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
  </li>
{% endfor %}
</ul>