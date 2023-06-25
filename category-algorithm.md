---
title: "Algorithm"
permalink: /categories/algorithm/
layout: archive
taxonomy: algorithm
---

{% assign posts = site.categories.algorithm %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}