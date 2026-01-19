---
permalink: categories/snuxi
title: "SNUXI - 학내 택시팟 모집 서비스"
layout: archive
toc: true
toc_sticky: true
author_profile: true
types: posts
---

{% assign posts = site.categories['SNUXI']%}
{% for post in posts %}
  {% include archive-single-2ver.html type=page.entries_layout %}
{% endfor %}