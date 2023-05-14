---
layout: page
title: This is the beginning of my documentation
---
<section class="page-section">
    <div class="container">
      <div class="row">
        <div class="col-md-3"> <!-- 25% column -->
          <h2 class="section-heading text-uppercase">Header 2</h2>
        </div>
        <div class="col-md-9"> <!-- 75% column -->
            <ul>
                {% for post in site.posts %}
                  <li>
                    <a href="{{ post.url }}">{{ post.title }}</a>
                  </li>
                {% endfor %}
              </ul>
        </div>
      </div>
    </div>
  </section>