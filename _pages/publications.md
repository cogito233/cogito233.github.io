---
layout: archive
title: "Publications"
permalink: /publications/
author_profile: true
---

This page is out of dated, you can find my articles on <u><a href="https://scholar.google.com/citations?user=74drf_cAAAAJ&hl=en">my Google Scholar profile</a>.</u>

{% include base_path %}

{% for post in site.publications reversed %}
  {% include archive-single.html %}
{% endfor %}
