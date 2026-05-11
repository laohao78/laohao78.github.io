---
layout: page
title: 硅基文明发展史
permalink: /silicon/
---

<p class="section-desc" style="margin-bottom: 24px;">四卷九篇，从图灵的纸带到文明觉醒。查看<a href="{{ site.baseurl }}/timeline">纪元年表 →</a></p>

<div class="posts">

{% assign silicon_all = site.posts | sort: "date" %}
{% assign current_volume = "" %}
{% for post in silicon_all %}
  {% if post.category == "硅基文明" %}
    {% if post.volume != current_volume %}
      {% assign current_volume = post.volume %}
      <div class="era-divider">
        <h2>{{ current_volume }}</h2>
      </div>
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
  {% endif %}
{% endfor %}

</div>
