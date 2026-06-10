---
layout: default
title: Blog
permalink: /blog/
---
<h1>Blog</h1>

<p class="blog-intro">Sometimes (more not than often), I dump some content here that may be considered useful or amusing (or both), depending on how you feel about my writing.</p>

<ul class="post-list">
{% for post in site.posts %}
  <li>
    <span class="post-meta">{{ post.date | date: "%m/%Y" }}</span>
    <a href="{{ post.url }}">{{ post.title }}</a>
  </li>
{% endfor %}
</ul>
