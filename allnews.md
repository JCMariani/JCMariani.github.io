---
title: "News"
subtitle: "All of them for eternity"
layout: page
permalink: /allnews.html
---

# News

{% for article in site.data.news %}
<p><b>{{ article.date }}</b> <br> {{ article.headline | markdownify}} <br> {{ article.extended | markdownify}}</p>
{% endfor %}
