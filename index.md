---
layout: page
title: Eat, sleep, work, repeat
tagline: Latest posts
---
{% include JB/setup %}

<div class="posts">
  {% for post in site.posts %}
  <h4>
    <span>{{ post.date | date_to_string }}</span>  &raquo;  
    <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a>
  </h4>
  {{post.description}}
  {% endfor %}
</div>
