---
layout: page
title: Device Management Series
permalink: /series/device-management/
---

# Device Management Series

This series covers different aspects of managing devices with Microsoft Intune and other MDM solutions.

{% assign series_posts = site.posts | where: "series", "device-management" | sort: "date" %}

{% if series_posts.size > 0 %}
<ol class="post-list">
  {% for post in series_posts %}
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
</ol>
{% else %}
<p>No device management articles yet. Check back soon!</p>
{% endif %}

## What's Covered in This Series

1. Device enrollment and troubleshooting common errors
2. Configuration profiles and policies
3. Application deployment and management
4. Security baseline configuration
5. Compliance policies and enforcement
6. Reporting and monitoring

Follow along as new articles are published to complete the series!