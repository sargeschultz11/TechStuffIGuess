---
layout: page
title: General IT Articles
permalink: /categories/general-it/
---

# General IT Articles

All posts related to general IT topics, troubleshooting, and solutions.

{% assign general_posts = site.posts | where_exp: "post", "post.categories contains 'general-it'" %}

{% if general_posts.size > 0 %}
<ul class="post-list">
  {% for post in general_posts %}
  <li>
    <h2>
      <a class="post-link" href="{{ post.url | relative_url }}">{{ post.title | escape }}</a>
    </h2>
    <span class="post-meta">{{ post.date | date: "%B %d, %Y" }}</span>
    {% if post.description %}
    <p>{{ post.description }}</p>
    {% endif %}
  </li>
  {% endfor %}
</ul>
{% else %}
<p>No general IT articles yet. Check back soon!</p>
{% endif %}