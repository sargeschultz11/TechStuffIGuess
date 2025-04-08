---
layout: page
title: Microsoft 365 Articles
permalink: /categories/microsoft365/
---

# Microsoft 365 Articles

All posts related to Microsoft 365 services, administration, and management.

{% assign m365_posts = site.posts | where_exp: "post", "post.categories contains 'microsoft365'" %}

{% if m365_posts.size > 0 %}
<ul class="post-list">
  {% for post in m365_posts %}
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
<p>No Microsoft 365 articles yet. Check back soon!</p>
{% endif %}