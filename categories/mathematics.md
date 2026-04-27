---
layout: page
title: Mathematics
permalink: /categories/mathematics/
---

Basic mathematics, measure theory, probability theory, stochastic differential equations, etc.

<ul>
{% for post in site.categories["Mathematics"] %}
  <li>
    <span>{{ post.date | date: "%Y-%m-%d" }}</span>
    <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
  </li>
{% endfor %}
</ul>