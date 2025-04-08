---
title: Home
layout: home
nav_order: 1
---
<!--<p>
My name is <b>Satrya Sabeni</b> and I’m a programmer with 5+ years experience in data science and software engineering, building image recognition models and data pipelines. The last few years I’ve also taken a strong interest in 3D applications, building rendering based applications and games, using technologies like openGL and Unreal Engine. This is the place where I document and share the interesting projects I’ve done. Feel free to check out my <a href="{{site.url}}/Portfolio.html">work</a> and <a href="{{site.url}}/Skillset.html">skills</a>.
</p>-->
<p>
Hi, I’m <b>Satrya Sabeni</b> — a programmer with over 5 years of experience in data science and software engineering, having worked on data and ML pipelines. In recent years, I’ve also focused on 3D applications, building rendering tools and games using technologies like OpenGL and Unreal Engine. This <a href="{{site.url}}/Portfolio.html">portfolio</a> showcases the personal projects I’ve worked on — feel free to explore!
</p>
---

{% for post in site.posts %}
{% if post.categories contains "front_page" %}
<div class="post">
    <div class="thumb">
        <a href="{{site.url}}{{post.url}}">
            <img src="{{site.url}}/assets/thumbnails/{{post.title | downcase | replace: ' ', '-' | replace: '.', '' }}.png">
            <span class="date-tag">{{post.date | date: "%Y-%m-%d" }}</span>
            <ul class="mini-tags">
            {% for tag in post.tags limit: post.num_tags %}
                <li>
                {{tag}}
                </li>
            {% endfor %}
            </ul>
        </a>
    </div>
    <div class="title-block">
        <a href="{{post.url}}">{{post.title}}</a>
    </div>
</div>
{% endif %}
{% endfor %}

