---
title: Portfolio
layout: default
nav_order: 2
---
# Portfolio
<hr>

## Graphics
<p>All works to do with Engines</p>
{% for post in site.posts %}
{% if post.categories contains "graphics" %}
<div class="post">
    <div class="thumb">
        <a href="{{site.url}}{{post.url}}">
            <img src="{{site.url}}/assets/thumbnails/{{post.title | downcase | replace: ' ', '-' | replace: '.', '' }}.png">
            <span class="date-tag">{{post.date | date: "%Y-%m-%d" }}</span>
        </a>
    </div>
    <div class="title-block">
        <a href="{{post.url}}">{{post.title}}</a>
    </div>
</div>
{% endif %}
{% endfor %}
<br/><br/>

<h2 style="display:block; float:left; width: 100%">Algorithms</h2>
<p>Work to do algorithm stuff</p>
{% for post in site.posts %}
{% if post.categories contains "algorithm" %}
<div class="post">
    <div class="thumb">
        <a href="{{site.url}}{{post.url}}">
            <img src="{{site.url}}/assets/thumbnails/{{post.title | downcase | replace: ' ', '-' | replace: '.', '' }}.png">
            <span class="date-tag">{{post.date | date: "%Y-%m-%d" }}</span>
        </a>
    </div>
    <div class="title-block">
        <a href="{{post.url}}">{{post.title}}</a>
    </div>
</div>
{% endif %}
{% endfor %}
<br/><br/>

<h2 style="display:block; float:left; width: 100%">Algorithms</h2>
<p style="float:left; width:100%;">Work to do Machine learning models and stuff</p>
{% for post in site.posts %}
{% if post.categories contains "ML" %}
<div class="post">
    <div class="thumb">
        <a href="{{site.url}}{{post.url}}">
            <img src="{{site.url}}/assets/thumbnails/{{post.title | downcase | replace: ' ', '-' | replace: '.', '' }}.png">
            <span class="date-tag">{{post.date | date: "%Y-%m-%d" }}</span>
        </a>
    </div>
    <div class="title-block">
        <a href="{{post.url}}">{{post.title}}</a>
    </div>
</div>
{% endif %}
{% endfor %}