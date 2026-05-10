---
layout: page
title: 纪元年表
permalink: /timeline/
---

<div class="timeline">

{% assign sorted_posts = site.posts | sort: "date" %}
{% assign current_volume = "" %}

{% for post in sorted_posts %}
  {% if post.volume != current_volume %}
    {% unless forloop.first %}</ul></div>{% endunless %}
    {% assign current_volume = post.volume %}
    <div class="timeline-era">
      <div class="era-marker"></div>
      <h2 class="era-title">{{ current_volume }}</h2>
      <ul class="era-posts">
  {% endif %}

  <li>
    <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a>
    <div class="timeline-date">{{ post.date | date: "%Y年%m月%d日" }}{% if post.era %} · {{ post.era }}{% endif %}</div>
  </li>

  {% if forloop.last %}</ul></div>{% endif %}
{% endfor %}

</div>
