---
title: "Creating a new website using Hugo"
date: 2022-01-05T12:28:47+01:00
tags: []
thumbnail: ""
summary: ""
draft: true
---

{{< figure src="/images/posts/2022/hugo/hugo.svg" alt="image" class="small" caption="figure-normal (without any classes)" >}}

<!-- ![Picture of the opening scene of Final Fantasy X, showing the Zanarkand Ruins](/images/posts/2022/hugo/hugo.svg "Zanarkand Ruins from Final Fantasy X") -->

As someone who's passionate about technology, I love to pick up new technologies. Some time ago, I read a blog post about how to create a personal website and how static websites were a great fit for that.

A static website is a pre-generated website where each page has a corresponding HTML page, just like it was done in the early days of the web. Nowadays we prefer dynamic websites with a back-end server that is generating pages on the fly because it offers more flexibility and a better user experience. However, for certain purposes static websites are a better fit.

The reason why I chose for a static website is because I heard from others that its perfect for a personal website: easy to maintain with the right framework, quick to build, secure, easier SEO (Search Engine Optimization), and so on. The framework that I decided to try out is [Hugo](https://gohugo.io/ "The world’s fastest framework for building websites").

Structure

-   Getting started on the Hugo website
-   Directly picked a theme that I liked and tested it out.
-   Followed the readme on the theme's Github.
-   Run locally.
-   Learned about how the static folder worked.
-   Moved config into folder structure with separate files.
-   Created a Github repo that acts as a Github Page.
-   Committed and pushed to Github, but this didn't actually put it online, since the repo contained the Hugo code, not HTML/CSS/JS.
-   I needed to generate static pages and put them on Github. Generating them offline and pushing them to the root of the Github repo is an option, but I'd rather have a way to automate.
-   Automated using Github Actions.
