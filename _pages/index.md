---
layout: splash
permalink: /
title: "Wade Swede"
header:
  overlay_color: "#1f3a4d"
  actions:
    - label: "See My Projects"
      url: "/projects/"
    - label: "Resources for Teachers"
      url: "/resources/"
excerpt: 'Linguist \| Educator'  # \| keeps Markdown from reading the pipe as a table
feature_row:
  - title: "AI & NLP Projects"
    excerpt: "Retrieval-augmented generation, language technology, speech synthesis, and machine learning."
    url: "/projects/#ai-nlp"
    btn_label: "Browse Projects"
    btn_class: "btn--primary"
  - title: "Educational Technology"
    excerpt: "Classroom applications and instructional tools, including games for Spanish instruction."
    url: "/projects/#edtech"
    btn_label: "Browse EdTech"
    btn_class: "btn--primary"
  - title: "Resources for Teachers"
    excerpt: "Reusable classroom tools, templates, prompts, AI agent skills, and professional development."
    url: "/resources/"
    btn_label: "Browse Resources"
    btn_class: "btn--primary"
---

{% include feature_row %}

{% assign featured = site.projects | where: "featured", true | sort: "order", "last" %}
{% if featured.size > 0 %}
<h2 class="home-section__title">Featured projects</h2>
{% include card-grid.html items=featured %}
{% endif %}

{% assign recent_resources = site.resources | sort: "last_updated", "first" | reverse %}
{% if recent_resources.size > 0 %}
<h2 class="home-section__title">New resources</h2>
{% assign recent_resources = recent_resources | slice: 0, 3 %}
{% include card-grid.html items=recent_resources %}
{% endif %}

{% if site.posts.size > 0 %}
<h2 class="home-section__title">Latest writing</h2>
{% for post in site.posts limit:3 %}{% include archive-single.html %}{% endfor %}
{% endif %}

<h2 class="home-section__title">Get in touch</h2>
<p>I'm always glad to connect with NLP practitioners and educators. Reach out by <a href="mailto:wade.swede91@gmail.com">email</a>, connect on <a href="https://www.linkedin.com/in/wade-swede-601baa99/">LinkedIn</a>, or find my code on <a href="https://github.com/wswede1">GitHub</a>.</p>
