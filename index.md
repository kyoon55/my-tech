---
layout: default
title: My Page
nav_order: 1
description: "Main Page."
---

Welcome to My Page

{% assign date = '2020-04-13T10:20:00Z' %}

- Original date - {{ date }}
- With timeago filter - {{ date | timeago }}

<ul>
  {% for post in site.posts %}
    <li>
      <a href="/my-tech{{ post.url }}">{{ post.title }}</a>
      {{ post.excerpt }}
    </li>
  {% endfor %}
</ul>

<h1>Test 2</h1>

<ul>
  {% for post in site.pages %}
    {% if post.path contains "doc" %}
    <li>
      <a href="/my-tech{{ post.url }}">{{ post.title }}</a>
    </li>
    {% endif %}
  {% endfor %}
</ul>

{% include title.html %}

{% include nav.html pages=site.html_pages key=nil %}

{% include nav.html pages=collection key=collection_key %}




{% for post in site.html_pages %}
{% endfor %}