---
title: Home
layout: home
nav_order: 1
---
<p>
Hi, my name is Satrya and currently I'm working as a Data Scientist where my main focus is building image recognition models mixed with some software and data engineering. Anything related to programming, algorithms, game dev, math and that type of stuff is interesting to me. This is a place where I'll post some of the things that I've worked on or found interesting. Plus it's just a very good exercise to document the things I do to reinforce my knowledge on these topics. To find out more about me in specifics check out the About page.
</p>
---

{% for post in site.posts %}
<div class="post">
    <div class="thumb">
        <a href="{{post.url}}">
            <img src="{{site.url}}/assets/thumbnails/{{post.title | downcase | replace: ' ', '-' }}.png">
        </a>
    </div>
    <div class="title-block">
        <a href="{{post.url}}">{{post.title}}</a>
    </div>
</div>
{% endfor %}
<!--<button>See all posts</button>-->


<!-- {% for post in posts %}
    {{ post.title }} asdf
{% endfor %} -->

[^1]: [It can take up to 10 minutes for changes to your site to publish after you push the changes to GitHub](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/creating-a-github-pages-site-with-jekyll#creating-your-site).

[Just the Docs]: https://just-the-docs.github.io/just-the-docs/
[GitHub Pages]: https://docs.github.com/en/pages
[README]: https://github.com/just-the-docs/just-the-docs-template/blob/main/README.md
[Jekyll]: https://jekyllrb.com
[GitHub Pages / Actions workflow]: https://github.blog/changelog/2022-07-27-github-pages-custom-github-actions-workflows-beta/
[use this template]: https://github.com/just-the-docs/just-the-docs-template/generate
