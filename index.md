---
layout: default
title: Home
nav_order: 1
description: "Main Page."
#permalink: /
---

# Focus on writing good documentation

Main Page
<h1>Welcome to my Blog</h1>

{% for page in site.page %}
  {% if page.path contains '/docs/' %}
    <h2><a href="{{ page.url }}">{{ page.title }}</a></h2>
    <p>{{ page.date | date: "%B %-d, %Y" }}</p>
    <p>{{ page.excerpt }}</p>
  {% endif %}
{% endfor %}