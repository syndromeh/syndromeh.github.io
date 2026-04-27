---
layout: page
title: Probability
permalink: /categories/probability/
---

<ul>
{% for post in site.categories["Probability"] %}
  <li>
    <span>{{ post.date | date: "%Y-%m-%d" }}</span>
    <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
  </li>
{% endfor %}
</ul>