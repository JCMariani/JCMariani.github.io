---
title: "News"
subtitle: "All of them for eternity"
layout: page
permalink: /allnews.html
---

# News

{% for article in site.data.news %}
<p><b>{{ article.date }}</b> <br line-height="120%"> 
  {{ article.headline | markdownify}} <br line-height="120%"> 

  {{ article.image | markdownify}} <br line-height="120%">

  {{ article.extended | markdownify}}</p>
{% endfor %}
