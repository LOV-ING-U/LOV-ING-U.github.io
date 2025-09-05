---
permalink: /javadesignpattern/
title: "Java Design Pattern"
layout: archive
toc: true
toc_sticky: true
author_profile: true
types: posts
---

{% assign posts = site.categories['java_design_patterns']%}
{% for post in posts %}
  {% include archive-single.html type=page.entries_layout %}
{% endfor %}