---
layout: page
title: Keep It Simple & Stupid
---
{% include JB/setup %}
    
## 文章列表

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date: "%Y-%m-%d" }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>