---
layout: page
title: Mac OS X Installations and setups
excerpt: "An archive of Mac OS X installation and setup instructions sorted by date."
image: tropwetlandmap
search_omit: true
---

<ul class="post-list">
{% for post in site.categories.setup-ide %}
  <li><ide-setup><a href="{{ site.url }}{{ post.url }}">{{ post.title }} <span class="entry-date"><time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%B %d, %Y" }}</time></span>{% if post.excerpt %} <span class="excerpt">{{ post.excerpt | remove: '\[ ... \]' | remove: '\( ... \)' | markdownify | strip_html | strip_newlines | escape_once }}</span>{% endif %}</a></ide-setup></li>
{% endfor %}
</ul>
