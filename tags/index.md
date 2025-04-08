---
layout: page
title: Tags
permalink: /tags/
---

# Article Tags

Browse all articles by tags.

{% comment %}
Create a list of all unique tags across all posts
{% endcomment %}

{% assign all_tags = "" | split: "," %}
{% for post in site.posts %}
  {% for tag in post.tags %}
    {% assign all_tags = all_tags | push: tag %}
  {% endfor %}
{% endfor %}
{% assign unique_tags = all_tags | uniq | sort %}

{% if unique_tags.size > 0 %}
<div class="tag-cloud">
  {% for tag in unique_tags %}
    {% assign tag_posts = site.posts | where_exp: "post", "post.tags contains tag" %}
    <a href="#{{ tag | slugify }}" class="tag-item" title="{{ tag_posts.size }} posts">
      {{ tag }} ({{ tag_posts.size }})
    </a>
  {% endfor %}
</div>

<div class="tag-list">
  {% for tag in unique_tags %}
    {% assign tag_posts = site.posts | where_exp: "post", "post.tags contains tag" %}
    <h2 id="{{ tag | slugify }}">{{ tag }}</h2>
    <ul>
      {% for post in tag_posts %}
        <li>
          <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
          <span class="post-date">({{ post.date | date: "%B %d, %Y" }})</span>
        </li>
      {% endfor %}
    </ul>
  {% endfor %}
</div>
{% else %}
<p>No tags found. Check back when more posts are published.</p>
{% endif %}

<style>
  .tag-cloud {
    margin: 2em 0;
    text-align: center;
  }
  
  .tag-item {
    display: inline-block;
    margin: 0.5em;
    padding: 0.3em 0.8em;
    background-color: #f0f0f0;
    border-radius: 3px;
    font-size: 0.9em;
    color: #333;
    text-decoration: none;
  }
  
  .tag-item:hover {
    background-color: #e0e0e0;
    color: #000;
  }
  
  .tag-list h2 {
    border-bottom: 1px solid #eaecef;
    padding-bottom: 0.3em;
    margin-top: 2em;
  }
  
  .post-date {
    color: #666;
    font-size: 0.9em;
  }