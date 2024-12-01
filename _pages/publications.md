---
layout: page
title: Publications
permalink: /publications/
---

<div class="publications">
  {% assign sorted_publications = site.publications | sort: "date" | reverse %}
  {% for publication in sorted_publications %}
  <div class="publication-item">
    <h2><a href="{{ publication.url }}" target="_blank">{{ publication.title }}</a></h2>
    <p>{{ publication.authors }}</p>
    {% if publication.venue %}
    <p><em>{{ publication.venue }}</em></p>
    {% endif %}
    <p>{{ publication.date | date: "%B %d, %Y" }}</p>
  </div>
  {% endfor %}
</div>
