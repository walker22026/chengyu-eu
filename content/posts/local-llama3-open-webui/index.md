---
title: "Running Llama 3 Locally with Open WebUI"
date: 2024-05-10T09:00:11+00:00
summary: "Pairing a consumer GPU running local Llama 3 with Open WebUI and One API to combine local and remote models behind one interface."
tags: ["LLM", "Self-Hosting", "AI"]
categories: ["Blog"]
featureImage: "https://images.chengyu.eu/file/8f4fcf55f870d046f93e0.png"
---

Got Llama 3 running locally on my consumer-grade GPU and set up Open WebUI as the interface for it. The install was easy — following the docs, a single Docker command was all it took, no real technical hurdles.

On the backend, I hooked it up to One API as an aggregation layer, with Cloudflare and Groq configured as upstream providers. Combined with the locally-hosted Llama 3 8B and 70B models, that gives me one unified API surface spanning both local and remote inference.

![Open WebUI interface](https://images.chengyu.eu/file/8f4fcf55f870d046f93e0.png)

![Local and remote models combined behind One API](https://images.chengyu.eu/file/671fceeae8a0198059f3f.png)
