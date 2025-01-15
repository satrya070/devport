---
title: Home
layout: home
nav_order: 1
---
<p>
Hi, I’m Satrya and I’m interested in anything to do with programming, math, graphics, and just <b>anything 3D</b>. I’ve studied computer science, and have 5+ years professional experience working as a data scientist building image recognition models and doing software engineering tasks.
<br/>
In the last years I’ve taken a strong interest in 3D applications, learning Unity, Unreal Engine, and currently diving into graphics programming now.
Feel free to check out some of the stuff I've built on below, and my skills on <a href="{{site.url}}/About/about.html">this</a> page.
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


[^1]: [It can take up to 10 minutes for changes to your site to publish after you push the changes to GitHub](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/creating-a-github-pages-site-with-jekyll#creating-your-site).

[Just the Docs]: https://just-the-docs.github.io/just-the-docs/
[GitHub Pages]: https://docs.github.com/en/pages
[README]: https://github.com/just-the-docs/just-the-docs-template/blob/main/README.md
[Jekyll]: https://jekyllrb.com
[GitHub Pages / Actions workflow]: https://github.blog/changelog/2022-07-27-github-pages-custom-github-actions-workflows-beta/
[use this template]: https://github.com/just-the-docs/just-the-docs-template/generate
