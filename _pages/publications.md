---
layout: archive
title: "Publications"
permalink: /publications/
author_profile: true
---

See also my [Google Scholar profile](https://scholar.google.com/citations?user=74drf_cAAAAJ&hl=en).

{% include base_path %}

<div class="pub-list">
{% assign pubs = site.publications | sort: 'date' | reverse %}
{% for pub in pubs %}
  <div class="pub">
    <a class="pub__title" href="{{ base_path }}{{ pub.url }}">{{ pub.title }}</a>
    <p class="pub__excerpt">{{ pub.excerpt | markdownify | strip_html | strip_newlines }}</p>
    <p class="pub__meta">{% if pub.venue and pub.venue != '' %}<span class="pub__venue">{{ pub.venue }}</span> &middot; {% endif %}{{ pub.date | date: "%Y" }}{% if pub.paperurl and pub.paperurl != '' %} &middot; <a href="{{ pub.paperurl }}">paper</a>{% endif %}</p>
  </div>
{% endfor %}
</div>
