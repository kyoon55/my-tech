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

{% for post in site.posts %}
  {% if post.path contains '/docs/' %}
    <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
    <p>{{ post.date | date: "%B %-d, %Y" }}</p>
    <p>{{ post.excerpt }}</p>
  {% endif %}
{% endfor %}