---
layout: default
title: Home
---

<section class="hero">
  <p class="eyebrow">Technical blog</p>
  <h1>Practical notes from building, debugging, and operating software systems.</h1>
  <p class="lead">I use this space to turn hard-won fixes, infrastructure experiments, and engineering notes into clear writeups that are useful later.</p>
</section>

<section class="intro-grid">
  <div>
    <h2>What I write about</h2>
    <p>Linux, infrastructure, developer tooling, secure access, and the small operational details that make systems easier to understand.</p>
  </div>
  <div>
    <h2>How I write</h2>
    <p>Each post aims to explain the context, the commands, the tradeoffs, and the troubleshooting path rather than only listing steps.</p>
  </div>
</section>

## Writing

{% for post in site.posts %}
<article class="post-card">
  <p class="post-date">{{ post.date | date: "%B %-d, %Y" }}</p>
  <h3><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h3>
  {% if post.summary %}
    <p>{{ post.summary }}</p>
  {% else %}
    <p>{{ post.excerpt | strip_html | truncate: 180 }}</p>
  {% endif %}
  {% if post.tags %}
    <p class="tags">
      {% for tag in post.tags %}
        <span>{{ tag }}</span>
      {% endfor %}
    </p>
  {% endif %}
</article>
{% endfor %}
