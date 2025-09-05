---
permalink: automata
title: "Automata Theory 내용 정리"
layout: archive
toc: true
toc_sticky: true
author_profile: true
types: posts
---

{% assign posts = site.categories['Automata Theory']%}
{% for post in posts %}
  {% include archive-single-2ver.html type=page.entries_layout %}
{% endfor %}