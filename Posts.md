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
    {{post.url}}
    </div>
{% endfor %}
</div>