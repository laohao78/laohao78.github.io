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

  <ul class="writing-list">
    {%- for c in site.data.collections %}
    {%- assign label = c[0] %}
    {%- assign meta = c[1] %}
    {%- assign posts = site[label] %}
    <li>
      <a href="{{ site.baseurl }}/{{ label }}">
        <span class="wl-name">{{ meta.name }}</span>
        <span class="wl-count">{{ posts.size }} 篇</span>
        <span class="wl-arrow">&rarr;</span>
        <span class="wl-desc">{{ meta.desc }}</span>
      </a>
    </li>
    {%- endfor %}
  </ul>
</div>
