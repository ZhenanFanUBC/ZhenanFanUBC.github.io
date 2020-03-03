---
layout: page
<!-- permalink: /publications/ -->
title: Publications
description: 
years: [Under Review, 2020, 2019]
---

{% for y in page.years %}
  <h3  id="{{y}}" class="pubyear">{{y}}</h3>
  {% bibliography -f papers -q @*[year={{y}}]* %}
{% endfor %}
