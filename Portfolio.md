---
title: Portfolio
layout: default
nav_order: 2
---
# Portfolio
<hr>
{% for post in site.posts %}
<div class="post">
    <div class="thumb">
        <a href="{{site.url}}{{post.url}}">
            <img src="{{site.url}}/assets/thumbnails/{{post.title | downcase | replace: ' ', '-' }}.png">
            <span class="date-tag">{{post.date | date: "%Y-%m-%d" }}</span>
        </a>
    </div>
    <div class="title-block">
        <a href="{{post.url}}">{{post.title}}</a>
    </div>
</div>
{% endfor %}