---
title: "Building My Own Information Feed Hub With TrendRadar"
date: 2026-05-04T21:00:13+00:00
summary: "Too many English finance and tech RSS feeds to skim by hand — TrendRadar, an open-source aggregator, filters, translates, and summarizes them via AI, then pushes the results to Telegram."
tags: ["Self-Hosting", "AI", "Tools"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/05/04/Pasted-image-20260504203658.jpg"
---

Aside from two days of overtime during the break, I spent the rest of it tinkering with an open-source project called TrendRadar — and it turned out to be worth writing up.

### Why I needed it

I subscribe to a fair number of English finance and tech RSS feeds — Yahoo Finance, Bloomberg, Seeking Alpha, Hacker News are all on the list. The problem is these sources put out a lot of content every day, and with English headlines, just skimming them takes real time, let alone figuring out which ones are worth reading closely.

What I actually wanted was simple: **tell me when there's something new, translate it for me, and give me a summary in the evening.** No need to babysit an RSS reader, and no need to open it and see a pile of yesterday's leftovers every time.

That's basically exactly what TrendRadar does.

### What is TrendRadar

TrendRadar is an open-source public-opinion/trend monitoring tool. Its core function is aggregating trending lists and RSS subscriptions across multiple platforms, then filtering, translating, and analyzing them with AI before pushing the results to your phone.

It supports a wide range of push channels — Feishu, DingTalk, Telegram, email, ntfy, Bark are all there. I use Telegram, since it's the simplest to set up.

The AI side runs on a unified LiteLLM interface, so in theory it supports basically any mainstream model provider. I connected it to Gemini Flash through my self-hosted LiteLLM gateway, which then forwards requests for TrendRadar to use.

### Deployment: still Docker on the NAS

My usual infrastructure philosophy: if it can run on the NAS, don't spin up a separate VPS for it. TrendRadar officially provides a Docker image, so I ran it directly on the DS218plus.

By default it crawls every 30 minutes, which is well within what a low-power machine like the DS218plus can handle without breaking a sweat.

### RSS configuration: mostly English finance and Portuguese news

The RSS configuration in `config.yaml` is just a matter of adding feeds as needed.

### AI translation: bilingual titles

TrendRadar supports AI translation, translating RSS titles into a target language and pushing both together. I've configured it for a "Chinese-foreign bilingual" mode.

### Push strategy: incremental during the day, AI summary at night

This was the part of the config that took the most time. The nice thing about incremental mode is that if there's nothing new between two runs, the program doesn't send any push at all. The evening AI summary, meanwhile, analyzes everything accumulated over the full day.

### Current state

After running it for a while, it's basically hitting what I wanted: scattered new-item pushes show up on my phone during the day without flooding it, and a single AI summary lands sometime after 7pm.
