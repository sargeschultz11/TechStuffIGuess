---
layout: page
title: Article Series
permalink: /series/
---

# Article Series

Browse collections of related articles organized by topic.

## Available Series

### [Device Management](/series/device-management.md)
A comprehensive look at device management with Microsoft Intune, from enrollment to advanced configuration.

{% comment %}
This section dynamically generates series based on your posts.
To add a new series, just use the series: series-name in your post front matter.
{% endcomment %}

{% assign series_posts = site.posts | where_exp: "post", "post.series" | map: "series" | uniq %}

{% if series_posts.size > 1 %}
## Other Series

{% for series_name in series_posts %}
  {% if series_name != "device-management" %}
    {% assign posts = site.posts | where: "series", series_name %}
    {% assign series_size = posts | size %}
    
### [{{ series_name | capitalize | replace: "-", " " }}](/series/{{ series_name }}/)
{{ series_size }} article{% if series_size != 1 %}s{% endif %}
  {% endif %}
{% endfor %}
{% endif %}