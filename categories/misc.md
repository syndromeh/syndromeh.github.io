---
layout: page
title: Misc
permalink: /categories/misc/
---

What I'm thinking.

<ul>
{% for post in site.categories["Misc"] %}
  <li>
    <span>{{ post.date | date: "%Y-%m-%d" }}</span>
    <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
  </li>
{% endfor %}
</ul>