---
permalink: /projects/
title: "Projects"
layout: single
classes: wide
author_profile: true
---

Case studies of my work in AI and natural language processing and in educational technology. Each one covers the problem, my approach, what I built, and what I learned.

{% assign projects = site.projects | sort: "order", "last" %}
{% for area in site.data.taxonomy.project_areas %}
{% assign items = projects | where: "project_area", area.id %}
<h2 id="{{ area.id }}">{{ area.title }}</h2>
<p>{{ area.description }}</p>
{% if items.size > 0 %}
{% include card-grid.html items=items %}
{% else %}
<p><em>Case studies coming soon.</em></p>
{% endif %}
{% endfor %}
