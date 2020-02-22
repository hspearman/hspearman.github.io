---
layout: page
title: Archives
order: 1
---

<div class="archives">
  <ul>
    {% for post in site.posts %}
      <li>
        <a href="{{ post.url }}">{{ post.title }}</a>
        <div>{{ post.date | date_to_string }}</div>
        <hr>
      </li>
    {% endfor %}
  </ul>
</div>