---
permalink: /writing/
title: "Writing"
layout: single
author_profile: true
---

Technical articles, tutorials, and reflections on applied AI and education.

{% if site.posts.size == 0 %}
<p><em>The first articles are coming soon.</em></p>
{% else %}
{% for post in site.posts %}{% include archive-single.html %}{% endfor %}
{% endif %}
