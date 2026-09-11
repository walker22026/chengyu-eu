---
title: "Fixing a Blank White Box Where Code Blocks Should Be in Typecho"
date: 2024-08-03T23:00:20+00:00
summary: "A CSS/plugin conflict was rendering code blocks as an ugly blank space — turned out to be a syntax-highlighting plugin fighting the theme."
tags: ["Typecho", "Troubleshooting"]
categories: ["Blog"]
featureImage: "https://images.chengyu.eu/file/37a35d534b9ab70091477.jpg"
---

Noticed by accident that any time my blog hit a code block, it rendered as an unfriendly, big blank white box.

Tried tracking it down with Chrome DevTools, tweaked the nginx config, tweaked the stylesheet — none of it fixed it.

Went through troubleshooting steps suggested by ChatGPT... and eventually landed on checking Typecho's plugins.

Turned out disabling the `ColorHighlight` syntax-highlighting plugin fixed the display immediately.

![Plugin settings](https://images.chengyu.eu/file/37a35d534b9ab70091477.jpg)

![Fixed code block display](https://images.chengyu.eu/file/b25f616a5bfe9bdb8d8ca.jpg)
