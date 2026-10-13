---
layout: page
title: 面试题
permalink: /interview/
---

这里汇总了我的面试题笔记：

{% assign items = site.pages | where_exp: "p", "p.path contains 'practise/'" | sort: "title" %}
<ul>
{% for p in items %}{% if p.title and p.url != page.url %}
  <li><a href="{{ p.url | relative_url }}">{{ p.title }}</a></li>
{% endif %}{% endfor %}
</ul>
