---
permalink: /os/
title: "Operating System 내용 정리"
layout: archive
toc: true
toc_sticky: true
author_profile: true
types: posts
---

{% assign posts = site.categories['operating_system']%}
{% for post in posts %}
  {% include archive-single.html type=page.entries_layout %}
{% endfor %}