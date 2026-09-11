---
title: "Migrating My Image Host From a Self-Built Google Drive Setup to Chevereto"
date: 2024-11-24T22:00:17+00:00
summary: "Swapping a 2TB Google Drive image host that needed re-authorizing every few days for a self-hosted Chevereto setup behind Cloudflare Tunnel."
tags: ["Self-Hosting", "NAS", "Cloudflare"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2024/11/24/Pasted-image-2024112420522212e7603a5abb71f0.jpg"
---

I recently reworked how my blog stores images, moving away from a Google-Drive-based system I'd built myself and switching to Chevereto. Google Drive offered a generous 2TB of space, but in practice it came with enough friction that I started looking for something better.

### Why I gave up on Google Drive

The obvious upside of using Google Drive as an image store is the sheer amount of space, but the downsides were just as real:

1. **Constant re-authorization.** Because of Google's API limits, I had to re-authorize every 2–3 days — genuinely annoying.
2. **Long-term auth is a hassle.** Getting a long-lived authorization means going through Google's review process, which is slow and complicated, and it took a lot of the shine off the whole setup.

I originally picked Google Drive for the storage headroom, but over time these issues piled up into a real annoyance, and I started looking for something more flexible and under my own control.

### Chevereto: a more self-sufficient setup

After some research I settled on Chevereto as the new image gallery tool. It's open source, capable, and — most importantly — fully under my own control. To make it work, I paired it with:

1. **Cloudflare Tunnel** — using Cloudflare's free tunnel service, I can reach my NAS securely without exposing my home network.
2. **NAS storage** — my home NAS became the storage backend, giving me flexible, controllable capacity and better data security.

Between the two, I ended up with an efficient, stable personal image host. Chevereto itself was easy to install and configure, and it comes with a solid feature set — multi-user management, batch uploads, automatic image optimization, and more.

![The Chevereto dashboard running on my own setup](https://media.chengyu.eu/images/2024/11/24/Pasted-image-2024112420522212e7603a5abb71f0.jpg)

### What the new setup gets me

Compared to the Google Drive approach, running Chevereto on my own NAS has some clear advantages:

1. **Full independence** — no third-party service to depend on, and no API limits to work around.
2. **Safer and more controllable** — Cloudflare Tunnel hides my real IP, and the data itself stays on storage I control rather than with an outside provider.
3. **Stable long-term** — no more constant re-authorization; the whole system is just more reliable.

![Images uploaded and served through the new pipeline](https://media.chengyu.eu/images/2024/11/24/Pasted-image-202411242052461bf7e2fcef1bf31d.jpg)
