---
layout: page
title: Projects
---

<div>
  <p>When I find the time, I like to make projects. It's not only a good exercise in creativity, but a great way for me to try new tech outside of work. You'll find a list of my past and present projects below.</p>
  <p>Most of my projects, along with some miscellaneous work, are available <a href="https://github.com/hspearman">here</a> on my Github account.</p>
</div>
<div class="projects">
  {% assign projects = site.projects | sort: 'date' %}
  {% for project in projects reversed %}
    <h2>{{ project.title }}</h2>
    <span class="project-date">{{ project.date | date: "%b %Y" }}</span>

    <div class="project-key-points">
      <div>
        {% if project.source != null %}
          <a href="{{ project.source }}"><i class="fa fa-code fa-fw" aria-hidden="true"></i>&nbsp;Source Code</a>
        {% else %}
          <div class="inactive-key-point"><i class="fa fa-ban fa-fw" aria-hidden="true"></i>&nbsp;Closed Source</div>
        {% endif %}
      </div>
      {% if project.site != null %}
        <div>
          <a href="{{ project.site }}"><i class="fa fa-link fa-fw" aria-hidden="true"></i>&nbsp;Site</a>
        </div>
      {% endif %}
      <div class="inactive-key-point"><i class="fa fa-cog fa-fw"></i>&nbsp;{{ project.tech }}</div>
    </div>
    {{ project.content }}
    <hr>
  {% endfor %}

</div>