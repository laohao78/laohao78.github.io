---
layout: page
title: 导航的发展史
permalink: /navigation/
---

<div class="posts posts-no-timeline">

{% assign posts = site.navigation | sort: "date" %}
{% for post in posts %}
  <article class="post">
    <h1><a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></h1>
    <div class="post-meta">
      <span class="post-date">{{ post.date | date: "%Y年%m月%d日" }}</span>
    </div>
    <div class="entry">{{ post.excerpt }}</div>
    <a href="{{ site.baseurl }}{{ post.url }}" class="read-more">—— 阅读全文 ——</a>
  </article>
{% endfor %}

{% if site.navigation.size == 0 %}
  <article class="post">
    <p style="color: #6a6458; font-style: italic;">文章即将发布，敬请期待。</p>
  </article>
{% endif %}

</div>
