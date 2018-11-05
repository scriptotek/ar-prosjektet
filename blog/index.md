---
layout: default
title: Library AR Project - Blog
---

<p>
  <a href="/">home</a> » blog
</p>

<div id="home">

{% for post in site.posts %}
  <h2 class="post-title"><a href="{{ post.url }}">{{ post.title }}</a></h2>
  <p class="post-meta">{{ post.date | date_to_string }}</p>
  <p class="post-excerpt">{{ post.excerpt | strip_html }}&hellip; (<a href="{{ post.url }}">Read More</a>)</p>
{% endfor %}


</div>