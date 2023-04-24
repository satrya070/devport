---
title: Posts
layout: default
nav_order: 2
---
<div>
{% for post in site.posts %}
    <div>
    {{post.title}}
    {{post.name}}
    <a href="{{post.url}}">link</a>
    </div>
{% endfor %}
</div>