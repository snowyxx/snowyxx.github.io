---
layout: page
title: 博客
permalink: /blogs/
---

{% for post in site.posts %}
  {% capture y %}{{post.date | date:"%Y"}}{% endcapture %}
  {% if year != y %}
    {% assign year = y %}
**{{ y }}**
  {% endif %}
  * {{ post.date | date: "%Y-%m-%d" }} &raquo; [ {{ post.title }} ]({{ post.url }})
{% endfor %}

