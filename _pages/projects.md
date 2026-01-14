---
layout: archive
title: "Projects"
permalink: /projects/
author_profile: true
---

{% include base_path %}

{% if site.author.github %}
<p>You can also find my projects on <a href="https://github.com/{{site.author.github}}" class="btn" target="_blank">my GitHub profile <i class="fab fa-github" aria-hidden="true"></i></a></p>
{% endif %}

{% assign sorted_portfolio = site.portfolio | sort: "order" %}
{% for post in sorted_portfolio %}
  {% include archive-single-portfolio.html %}
{% endfor %}
