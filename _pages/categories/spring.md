---
permalink: /spring/
title: "Spring"
layout: archive
toc: true
toc_sticky: true
author_profile: true
types: posts
---

{% assign posts = site.categories['spring']%}
{% for post in posts %}
  {% include archive-single.html type=page.entries_layout %}
{% endfor %}