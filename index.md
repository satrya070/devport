---
title: Home
layout: home
nav_order: 1
---

Hello, my name Satrya and .. interest data science, game dev, more about me here. This just a place where I'd just like to save some of the things that I have been doing and found interesting. Plus it's just a very good exercise to document these things to reinforcing my knowledge on these topics.
<p>
{% for post in site.posts %}
    <div>
    {{post.title}}
    </div>
{% endfor %}
</p>

<!-- {% for post in posts %}
    {{ post.title }} asdf
{% endfor %} -->

<!-- This is a *bare-minimum* template to create a Jekyll site that uses the [Just the Docs] theme. You can easily set the created site to be published on [GitHub Pages] – the [README] file explains how to do that, along with other details. -->

<!-- If [Jekyll] is installed on your computer, you can also build and preview the created site *locally*. This lets you test changes before committing them, and avoids waiting for GitHub Pages.[^1] And you will be able to deploy your local build to a different platform than GitHub Pages. -->


----

[^1]: [It can take up to 10 minutes for changes to your site to publish after you push the changes to GitHub](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/creating-a-github-pages-site-with-jekyll#creating-your-site).

[Just the Docs]: https://just-the-docs.github.io/just-the-docs/
[GitHub Pages]: https://docs.github.com/en/pages
[README]: https://github.com/just-the-docs/just-the-docs-template/blob/main/README.md
[Jekyll]: https://jekyllrb.com
[GitHub Pages / Actions workflow]: https://github.blog/changelog/2022-07-27-github-pages-custom-github-actions-workflows-beta/
[use this template]: https://github.com/just-the-docs/just-the-docs-template/generate
