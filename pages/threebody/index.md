---
layout: page
title: 三体
permalink: /threebody/
---

<div class="posts posts-no-timeline">

{% assign all = site.threebody | sort: "date" | reverse %}
{% for post in all %}
  <article class="post">
    <h1><a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></h1>
    <div class="post-meta">
      <span class="post-date">{{ post.date | date: "%Y年%m月%d日" }}</span>
    </div>
    <div class="entry">{{ post.excerpt }}</div>
    <a href="{{ site.baseurl }}{{ post.url }}" class="read-more">—— 阅读全文 ——</a>
  </article>
{% endfor %}

{% if all.size == 0 %}
  <article class="post">
    <p style="color: #6a6458; font-style: italic;">文章即将发布。</p>
  </article>
{% endif %}

</div>
