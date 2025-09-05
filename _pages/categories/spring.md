---
permalink: spring
title: "Spring"
layout: archive
toc: true
toc_sticky: true
author_profile: true
types: posts
---

{% assign posts = site.categories.Spring %}
{% for post in posts %}
  {% include archive-single-2ver.html type=page.entries_layout %}
{% endfor %}