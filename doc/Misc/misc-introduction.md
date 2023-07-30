---
layout: default
title: Misc
has_children: true
permalink: /docs/Misc
excerpt: Front page of documents uncategorized
date: 2023-03-01
---
<h1>{{ page.title }}</h1>
<h2>{{ page.excerpt }}</h2>
<h3>Date Posted: {{ page.date | date: "%Y %m %d" }}</h3>

<details>
<summary>Summary</summary>
{% highlight python3 %}
Code
{% endhighlight %}