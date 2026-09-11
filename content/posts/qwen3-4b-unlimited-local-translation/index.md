---
title: "Running Unlimited AI Translation Locally with Qwen3 4B"
date: 2025-05-09T18:00:21+00:00
summary: "Pairing a small local Qwen3 4B model with Immersive Translate for a private, uncapped translation setup — plus a prompt trick that speeds it up."
tags: ["AI", "Self-Hosting", "Ollama"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2025/05/09/2025-05-09-16.47.08.jpg"
---

I deployed a self-hosted immersive translation system built on the Qwen3 4B small model locally, and ended up with an AI translation experience I can use without any usage caps.

### Why Qwen3 4B?

Compared to the larger model variants, Qwen3 4B strikes a decent balance between reasoning ability and resource use. It runs smoothly on an ordinary consumer GPU or even a reasonably powerful VPS, which makes it a good fit for anyone who wants to self-host their own private translation service.

### Installing Ollama and Open WebUI

I won't go through the install process in detail here — pull the `qwen3:4b` model, then grab an API key from inside Open WebUI.

![Setting up Ollama and Open WebUI](https://media.chengyu.eu/images/2025/05/09/2025-05-09-16.47.08.jpg)

### Configuring Immersive Translate

Point Immersive Translate at a custom API endpoint.

![Custom API configuration in Immersive Translate](https://media.chengyu.eu/images/2025/05/09/2025-05-09-16.47.47.jpg)

**Speeding up translation:** adding the keyword `nothink` to the translation prompt meaningfully cuts down the model's "thinking" time and speeds up responses — especially noticeable with immersive, sentence-by-sentence translation. The keyword itself doesn't change the semantics of the translation, it just nudges the model toward translating directly rather than reasoning its way there.

![The nothink keyword speeding up responses](https://media.chengyu.eu/images/2025/05/09/2025-05-09-17.03.13.jpg)
