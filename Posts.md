---
title: Posts
layout: default
nav_order: 2
---
All my posts!
{% for post in site.posts %}
<div class="post">
    <div class="thumb">
        <a href="{{post.url}}">
            <img src="{{site.url}}/assets/thumbnails/advent-day16.png">
        </a>
    </div>
    <div class="title-block">
        <a href="{{post.url}}">{{post.title}}</a>
    </div>
</div>
{% endfor %}