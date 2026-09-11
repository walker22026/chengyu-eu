---
title: "My Free Image Host Died Overnight — and How I Rebuilt It"
date: 2024-09-07T23:16:00+00:00
summary: "Telegram quietly killed Telegraph's image-hosting support, breaking the free image pipeline my blog depended on — here's how I patched it back together in a day."
tags: ["Self-Hosting", "Telegram", "Troubleshooting"]
categories: ["Blog"]
featureImage: "https://image.chengyu.eu/drive/1OOQeolTRBgL5pgxkhz7vEDQ2sgz0xRcE"
---

### The setup, before it broke

My blog originally ran on a server provided through a university program, with a fairly limited bandwidth quota. To keep that quota from getting eaten up, I moved all my images off to an external host rather than serving them from the main server.

To solve the "where do I host images cheaply" problem, I went with a slightly unusual setup: Cloudflare in front, and Telegram's Telegraph service as the actual image store behind it. Using the open-source **Telegraph-Image** project on GitHub, I could upload images straight to Telegram's Telegraph service without much extra cost. For safety, I even contributed a fix upstream — adding a restriction on which domains were allowed to upload, to stop the mechanism being abused by randoms.

On top of that, I used **Obsidian** to automate image uploads and fast blog posting. The whole pipeline made my workflow genuinely smooth — I was using it to manage and publish almost every day.

### Then, on a Friday night, it all stopped

Every attempt to upload an image started throwing errors. Fairly stressful timing — I went straight to the relevant discussion groups looking for answers, and found plenty of other people hitting the exact same problem.

### The official word: it's over

After some digging around in Telegram-related groups, I found the root cause: Telegram had officially discontinued Telegraph's support for image storage. That meant the image-hosting method I'd been relying on was, from that point on, history.

The timing made me think of the recent arrest of Telegram founder Pavel Durov — I have no idea whether the two are actually connected, and there's no official statement linking them, but the timing was unsettling regardless.

### Rebuilt, and back in business

Spent most of Saturday fixing this. Fortunately I found a replacement fairly quickly, rebuilt a free image host, and got the auto-posting pipeline working again.

If you're reading this post, it means everything's running again — the new image host is up, and the blog's full automated pipeline is back to normal. A small win, coming out of an otherwise annoying outage.

![The new image hosting pipeline back up and running](https://image.chengyu.eu/drive/1OOQeolTRBgL5pgxkhz7vEDQ2sgz0xRcE)
