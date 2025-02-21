---
title: Home
layout: home
nav_order: 1
---
<!--<p>
Hi, I’m Satrya and I’m interested in anything to do with programming, math, graphics, and just anything <b>3D</b>. I’ve studied computer science, and have 5+ years professional experience working as a data scientist building image recognition models and doing software engineering tasks.
<br/>
In the last years I’ve taken a strong interest in 3D applications, learning Unity, Unreal Engine, and currently diving into graphics programming now.
Feel free to check out some of the stuff I've built on below, and my skills on <a href="{{site.url}}/About/about.html">this</a> page.
</p>-->
<p>
My name is <b>Satrya Sabeni</b> and I’m a programmer with 5+ years experience in data science and software engineering, building image recognition models and data pipelines. The last few years I’ve also taken a strong interest in 3D applications, building rendering based applications and games, using technologies like openGL and Unreal Engine. This is the place where I document and share the interesting projects I’ve done. Feel free to check out my <a href="{{site.url}}/Portfolio.html">work</a> and <a href="{{site.url}}/Skillset.html">skills</a>.
</p>
---

{% for post in site.posts %}
{% if post.categories contains "front_page" %}
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
<!--<button>See all posts</button>-->
