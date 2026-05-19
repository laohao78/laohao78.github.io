---
layout: page
title: 硅基文明发展史
permalink: /silicon/
---

<nav class="sub-nav">
  <a href="{{ site.baseurl }}/silicon/timeline">纪元年表</a>
  <a href="{{ site.baseurl }}/silicon/about">关于这部文明史</a>
</nav>

<div class="volume-list">

{% assign silicon_all = site.silicon | sort: "date" %}
{% assign current_volume = "" %}
{% for post in silicon_all %}
  {% if post.volume != current_volume %}
    {% unless forloop.first %}</div>{% endunless %}
    {% assign current_volume = post.volume %}
    <details class="volume-group"{% if forloop.first %} open{% endif %}>
      <summary class="volume-header">
        <h2>{{ current_volume }}</h2>
        <span class="volume-toggle"></span>
      </summary>
      <div class="volume-posts">
  {% endif %}

  <article class="post">
    <h1><a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></h1>
    <div class="post-meta">
      <span class="post-date">{{ post.date | date: "%Y年%m月%d日" }}</span>
      {% if post.era %}<span class="era-tag">{{ post.era }}</span>{% endif %}
    </div>
    <div class="entry">{{ post.excerpt }}</div>
    <a href="{{ site.baseurl }}{{ post.url }}" class="read-more">—— 阅读全文 ——</a>
  </article>

  {% if forloop.last %}</div></details>{% endif %}
{% endfor %}

</div>
