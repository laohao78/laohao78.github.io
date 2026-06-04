---
layout: page
title: 专栏
permalink: /writing/
wide: true
---

<div class="writing-page">
  <section class="writing-intro">
    <p class="writing-kicker">Writing</p>
    <h2>专栏</h2>
    <p>这里收纳长期写作：一条主线讲硅基文明，一组侧栏记录 AI、机器人、方法论和对话。</p>
  </section>

<div class="writing-hero">
  <div>
    <p class="writing-kicker">Featured Series</p>
    <h2><a href="{{ site.baseurl }}/silicon">硅基文明发展史</a></h2>
    <p>从第一行代码到文明觉醒。一部以硅基生命为主角的编年史。</p>
    <p class="writing-hero-links">
      <a href="{{ site.baseurl }}/silicon">浏览全部 &rarr;</a>
      <a href="{{ site.baseurl }}/silicon/timeline">纪元年表</a>
      <a href="{{ site.baseurl }}/silicon/about">关于这部文明史</a>
    </p>
  </div>
  <ul class="writing-hero-list">
    {% assign silicon_all = site.silicon | sort: "date" | reverse %}
    {% for post in silicon_all limit: 5 %}
      <li><a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
  </ul>
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

  <div class="writing-card">
    <h3><a href="{{ site.baseurl }}/notes">技术笔记</a></h3>
    <p class="writing-card-desc">ROS、SLAM、仿真——实战踩坑与解法。</p>
    <ul>
      {% assign notes = site.notes | sort: "date" | reverse %}
      {% for post in notes %}
        <li><a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></li>
      {% endfor %}
    </ul>
  </div>

  <div class="writing-card">
    <h3><a href="{{ site.baseurl }}/fangfalun">方法论</a></h3>
    <p class="writing-card-desc">思维方式、工作法则。从第一性原理到执行纪律。</p>
    <ul>
      {% assign methods = site.fangfalun | sort: "date" | reverse %}
      {% for post in methods %}
        <li><a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></li>
      {% endfor %}
    </ul>
  </div>

  <div class="writing-card">
    <h3><a href="{{ site.baseurl }}/talk">与优秀的人同行</a></h3>
    <p class="writing-card-desc">对话访谈。那些在机器人、AI、SLAM 领域做出好东西的人。</p>
    <ul>
      {% assign talks = site.talk | sort: "date" | reverse %}
      {% for post in talks %}
        <li><a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></li>
      {% endfor %}
    </ul>
  </div>

  <div class="writing-card">
    <h3><a href="{{ site.baseurl }}/daohang">导航的发展史</a></h3>
    <p class="writing-card-desc">从 Sputnik 到 GPS 到卡尔曼滤波。在这颗星球上寻找自己的位置。</p>
    <ul>
      {% assign posts = site.daohang | sort: "date" | reverse %}
      {% for post in posts %}
        <li><a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></li>
      {% endfor %}
    </ul>
  </div>
</div>
</div>
