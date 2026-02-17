---
layout: default
permalink: /about/
title: About
---

<div class="about-content" markdown="1">

# about

hi i'm chris

currently based in Seoul, South Korea

this is my mind dump for everything i'm learning

## skills

<div class="skills-grid">
{% for skill in site.skills %}
<span class="skill-tag">{{ skill }}</span>
{% endfor %}
</div>

## certs

<ul class="cert-list">
{% assign sorted_certs = site.certifications | sort: 'date' | reverse %}
{% for cert in sorted_certs %}
<li class="cert-item">
  <a href="{{ cert.url | relative_url }}" class="cert-name">{{ cert.name }}</a>
  <span class="cert-year">{{ cert.year }}</span>
</li>
{% endfor %}
</ul>

</div>
