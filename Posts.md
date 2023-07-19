---
title: Posts
layout: default
nav_order: 2
---
All my posts!
{% for post in site.posts %}
<div class="post">
    <div class="thumb">
        <a href="{{site.url}}{{post.url}}">
            <img src="{{site.url}}/assets/thumbnails/{{post.title | downcase | replace: ' ', '-' }}.png">
        </a>
    </div>
    <div class="title-block">
        <a href="{{site.url}}{{post.url}}">{{post.title}}</a>
    </div>
</div>
{% endfor %}