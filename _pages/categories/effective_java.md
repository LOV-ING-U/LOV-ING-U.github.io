---
permalink: categories/effectivejava
title: "Effective Java"
layout: archive
toc: true
toc_sticky: true
author_profile: true
types: posts
---

{% assign posts = site.categories['Effective java']%}
{% for post in posts %}
  {% include archive-single-2ver.html type=page.entries_layout %}
{% endfor %}