---
layout: archive
title: "Publications"
permalink: /publications/
author_profile: true
---

{% include base_path %}

{% if site.author.googlescholar %}
<p>You can also find my articles on <a href="{{site.author.googlescholar}}" class="btn">my Google Scholar profile <i class="ai ai-google-scholar" aria-hidden="true"></i></a></p>
{% endif %}

{% for post in site.publications reversed %}
  {% include archive-single.html %}
{% endfor %}
