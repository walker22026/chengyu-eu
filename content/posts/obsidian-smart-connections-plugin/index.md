---
title: "Trying Out Smart Connections: an AI Plugin for Obsidian"
date: 2024-08-08T11:00:18+00:00
summary: "Chatting with my own notes via a local model, plus a gotcha when pointing the plugin at a self-hosted One API server."
tags: ["Obsidian", "AI", "Self-Hosting"]
categories: ["Blog"]
featureImage: "https://images.chengyu.eu/file/16a07728074d5379f9ea9.jpg"
---

Installed the **Smart Connections** plugin in Obsidian today. It's an AI-powered plugin for Obsidian that lets you chat with your notes through embedding-based AI, and automatically surfaces related notes based on the current one. It supports both local models and well over 100 different API-based models — Claude, Gemini, ChatGPT, Llama 3, and more.

### First impressions

I'm running `llama-3.1-8b-instant`, which handles normal conversation fine. What's genuinely useful is that it can summarize content pulled from my own past posts.

![Summarizing my cycling posts](https://images.chengyu.eu/file/16a07728074d5379f9ea9.jpg)

![Summarizing my investing posts](https://images.chengyu.eu/file/3a450978add622ba2c5a5.jpg)

### Pointing it at a self-hosted server

Configuring it against OpenAI's own API directly is straightforward, but pointing it at my self-hosted One API server kept throwing errors, and the plugin's own error messages weren't very informative. After digging in, it turns out it needs the standard three-part endpoint configuration — once set up that way, it works fine.

### What it's for

Smart Connections is built for individuals who want AI to strengthen how they manage notes and surface connections in their own knowledge base. It supports local models as well as API models like Anthropic's Claude, Google's Gemini, and OpenAI's GPT-4. Through its Smart Chat feature you can have a conversation with your notes, and save that conversation as a note or a canvas.
