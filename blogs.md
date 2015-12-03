---
layout: page
title: 博客
permalink: /blogs/
---

{% for post in site.posts %}
  * {{ post.date | date: "%Y-%m-%-d" }} &raquo; [ {{ post.title }} ]({{ post.url }})
{% endfor %}