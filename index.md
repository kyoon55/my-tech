---
layout: default
title: Home
nav_order: 1
description: "Main Page."
#permalink: /
---

Welcome to My Home Page

{% assign date = '2020-04-13T10:20:00Z' %}

- Original date - {{ date }}
- With timeago filter - {{ date | timeago }}