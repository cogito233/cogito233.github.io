---
layout: archive
title: "Blog"
permalink: /blog/
author_profile: true
---

Occasional notes on evaluation, agents, and working with them. Posts are in English; some carry a Chinese version below the fold.

{% include base_path %}

<div class="pub-list">
{% assign posts = site.posts | sort: 'date' | reverse %}
{% for post in posts %}
  <div class="pub">
    <a class="pub__title" href="{{ base_path }}{{ post.url }}">{{ post.title }}</a>
    <p class="pub__excerpt">{{ post.excerpt | markdownify | strip_html | strip_newlines }}</p>
    <p class="pub__meta">{{ post.date | date: "%B %Y" }}</p>
  </div>
{% endfor %}
</div>
