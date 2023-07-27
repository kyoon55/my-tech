---
layout: default
title: Home
nav_order: 1
description: "Main Page."
permalink: /
---

# Focus on writing good documentation

Main Page

<div class="home" id="home">
	<h1 class="pageTitle">Recent Posts</h1>
	<div class="posts noList">
		{%- for docs in paginator.docs -%}
		<article>
			<span class="date">{{ post.date | date: '%B %d, %Y' }}</span>
			<h3>
				<a class="post-link" href="{{ post.url }}">{{ post.title }}</a>
			</h3>
			<p>
				{%- if post.description -%}{{ post.description }}{%- else -%}{{
				post.excerpt | strip_html }}{%- endif -%}
			</p>
		</article>
		{%- endfor -%}
	</div>
	<!-- Pagination links -->
	<div class="pagination">
		{%- if paginator.previous_page -%}
		<a
			href="{{ paginator.previous_page_path }}"
			class="previous button__outline"
			>Newer Posts</a
		>
		{%- endif -%} {%- if paginator.next_page -%}
		<a href="{{ paginator.next_page_path }}" class="next button__outline"
			>Older Posts</a
		>
		{%- endif -%}
	</div>
</div>


<!-- This loops through the paginated posts -->
{% for post in paginator.posts %}
  <h1><a href="{{ post.url }}">{{ post.title }}</a></h1>
  <p class="author">
    <span class="date">{{ post.date }}</span>
  </p>
  <div class="content">
    {{ post.content }}
  </div>
{% endfor %}

<!-- Pagination links -->
<div class="pagination">
  {% if paginator.previous_page %}
    <a href="{{ paginator.previous_page_path }}" class="previous">
      Previous
    </a>
  {% else %}
    <span class="previous">Previous</span>
  {% endif %}
  <span class="page_number ">
    Page: {{ paginator.page }} of {{ paginator.total_pages }}
  </span>
  {% if paginator.next_page %}
    <a href="{{ paginator.next_page_path }}" class="next">Next</a>
  {% else %}
    <span class="next ">Next</span>
  {% endif %}
</div>