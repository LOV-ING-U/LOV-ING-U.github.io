---
permalink: categories/os
title: "Operating System 내용 정리"
layout: archive
toc: true
toc_sticky: true
author_profile: true
types: posts
---

{% assign posts = site.categories['Operating System']%}
{% for post in posts %}
  {% include archive-single-2ver.html type=page.entries_layout %}
{% endfor %}