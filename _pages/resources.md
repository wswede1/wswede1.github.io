---
permalink: /resources/
title: "Resources"
layout: single
classes: wide
author_profile: true
---

Free tools and materials for teachers and developers: classroom tools, templates, prompts, AI agent skills, and professional development sessions.

{% assign resources = site.resources | sort: "order", "last" %}
{% if resources.size == 0 %}
<p><em>Resources are on the way. Here's what this page will include:</em></p>
<ul>
{% for t in site.data.taxonomy.resource_types %}<li><strong>{{ t.title }}</strong>: {{ t.description }}</li>
{% endfor %}
</ul>
{% else %}
{% for t in site.data.taxonomy.resource_types %}
{% assign items = resources | where: "resource_type", t.id %}
{% if items.size > 0 %}
<h2 id="{{ t.id }}">{{ t.title }}</h2>
<p>{{ t.description }}</p>
{% include card-grid.html items=items %}
{% endif %}
{% endfor %}
{% endif %}
