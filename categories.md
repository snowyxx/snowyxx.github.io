---
layout: page
title: 分类
permalink: /categories/
---
<ul class="categories">
 {% for categorie in site.categories %}
<li><a href="#{{ categorie[0] }}">{{ categorie[0] }}</a><sup>{{ categorie[1].size }}</sup></li>
{% endfor %}     
</ul>





<ul class="listing">
{% for categorie in site.categories %}
  <li class="listing-seperator" id="{{ categorie[0] }}">{{ categorie[0] }}</li>
{% for post in categorie[1] %}
  <li class="listing-item">
  <time datetime="{{ post.date | date:"%Y-%m-%d" }}">{{ post.date | date:"%Y-%m-%d" }}</time>
  <a href="{{ post.url }}" title="{{ post.title }}">{{ post.title }}</a>
  </li>
{% endfor %}
{% endfor %}
</ul>