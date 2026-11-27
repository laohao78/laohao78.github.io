---
layout: page
title: 专栏
permalink: /writing/
wide: true
description: "长期写作的专栏：硅基文明发展史、导航的发展史、AI 观察、技术笔记与访谈。"
---

<div class="writing-page">
  <section class="writing-intro">
    <p class="writing-kicker">Writing</p>
    <h2>专栏</h2>
    <p>长期写作：一条主线讲硅基文明，一组侧栏记录 AI、机器人、方法论和对话。</p>
  </section>

  <div class="card-grid">
    {%- for c in site.data.collections %}
    {%- assign label = c[0] %}
    {%- assign meta = c[1] %}
    {%- assign posts = site[label] %}
    <a class="card" href="{{ site.baseurl }}/{{ label }}">
      <span class="card-index">0{{ forloop.index }}</span>
      <span class="card-title">{{ meta.name }}</span>
      <span class="card-desc">{{ meta.desc }}</span>
      <span class="card-meta">{{ posts.size }} 篇 · 阅读 &rarr;</span>
    </a>
    {%- endfor %}
  </div>
</div>
