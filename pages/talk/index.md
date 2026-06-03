---
layout: page
title: 与优秀的人同行
permalink: /talk/
---

<div class="posts posts-no-timeline">

{% assign talks = site.talk | sort: "date" | reverse %}
{% for post in talks %}
  <article class="post">
    <h1><a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></h1>
    <div class="post-meta">
      <span class="post-date">{{ post.date | date: "%Y年%m月%d日" }}</span>
      <span style="color: var(--star-blue); margin-left: 12px;">嘉宾：{{ post.guest }}</span>
    </div>
    <div class="entry">{{ post.excerpt }}</div>
    <a href="{{ site.baseurl }}{{ post.url }}" class="read-more">—— 阅读全文 ——</a>
  </article>
{% endfor %}

{% if site.talk.size == 0 %}
  <article class="post">
    <p style="color: #6a6458; font-style: italic;">访谈即将发布，敬请期待。</p>
  </article>
{% endif %}

</div>
