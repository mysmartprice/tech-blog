---
layout: page
title: Our Team updates
excerpt: "Updates about our Team"
---

<ul class="post-list">
{% for post in site.categories.team %} 
  <li><article><a href="{{ site.url }}{{ post.url }}">{{ post.title }} <span class="entry-date"><time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%B %d, %Y" }}</time></span></a></article></li>
{% endfor %}
</ul>
