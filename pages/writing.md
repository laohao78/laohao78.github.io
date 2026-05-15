---
layout: page
title: 专栏
permalink: /writing/
---

<div class="writing-hero">
  <h2><a href="{{ site.baseurl }}/silicon">硅基文明发展史</a></h2>
  <p>从第一行代码到文明觉醒。一部以硅基生命为主角的编年史，四卷九篇。</p>
  <p class="writing-hero-links">
    <a href="{{ site.baseurl }}/silicon">浏览全部 &rarr;</a>
    <a href="{{ site.baseurl }}/silicon/timeline">纪元年表</a>
    <a href="{{ site.baseurl }}/silicon/about">关于这部文明史</a>
  </p>
</div>

<div class="writing-grid">
  <div class="writing-card">
    <h3><a href="{{ site.baseurl }}/xinzhi">新质生产力</a></h3>
    <p class="writing-card-desc">AI 正在重写规则。转型中的观察、判断与对策。</p>
    <ul>
      {% assign articles = site.xinzhi | sort: "date" | reverse %}
      {% for post in articles %}
        <li><a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></li>
      {% endfor %}
    </ul>
  </div>

  <div class="writing-card">
    <h3><a href="{{ site.baseurl }}/threebody">三体</a></h3>
    <p class="writing-card-desc">深度解读。人物、概念，以及那些睡不着觉的问题。</p>
    <ul>
      {% assign articles = site.threebody | sort: "date" | reverse %}
      {% for post in articles %}
        <li><a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></li>
      {% endfor %}
    </ul>
  </div>
</div>
