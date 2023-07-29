---
layout: default
title: My Page
nav_order: 1
description: "Main Page."
---

<h2>Welcome to My Page, Here is a list of my posts</h2>
<ul>
	{% assign postdocs = site.pages | sort: 'date' | reverse %}
	{% for post in postdocs limit:8%}
    {% if post.path contains "doc" %}
		<article>
			<h3>
				<a class="post-link" href="/my-tech{{ post.url }}">{{ post.title }}</a>
				
			</h3>
			<h5><span class="date">{{ post.date | date_to_string }}</span></h5>
			<p>
				{%- if post.description -%}{{ post.description }}{%- else -%}{{
				post.excerpt | strip_html }}{%- endif -%}
			</p>
		</article>
    {% endif %}
		{% endfor %}
</ul>