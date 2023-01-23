---
title: "iOS"
permalink: /ios
layout: category
---

{% assign posts = site.categories.ios %}
{% for post in posts %}
{% include custom-archive-single.html type=entries_layout %}
{% endfor %}