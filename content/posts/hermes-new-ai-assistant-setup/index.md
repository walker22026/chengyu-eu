---
title: "Hermes: Setting Up My New AI Assistant"
date: 2026-04-19T01:00:07+00:00
summary: "OpenClaw's buzz is fading and everyone's talking about Hermes — its token compression alone (50%+, reportedly up to 70% in some cases) was reason enough to spin up a VPS and try it."
tags: ["Self-Hosting", "AI"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/04/18/Pasted-image-20260419005308.jpg"
---

OpenClaw's popularity is visibly cooling off, and lately a lot of people have been talking about using Hermes instead. I looked into this agent tonight, and setting everything else aside, what caught my attention most was its token consumption:

1. Its token compression ratio can reach over 50%.
2. Reportedly, in certain scenarios it can even hit 70%.

That's a big draw for me. As everyone knows, the biggest problem with using OpenClaw is its enormous token consumption. There's a joke going around that some company used an LLM to replace certain human functions, but ended up spending more on the model than they would have on hiring people. So being able to deliver the same functionality at the lowest possible cost is where the real strength lies.

Another factor is that I think it fits my needs better — Hermes feels more like a work assistant, better suited to helping me with my actual work. By comparison, OpenClaw is "heavier," relying more on calling a large number of external capabilities and extensions.

As the saying goes, "adults don't make choices — they take both," so today I grabbed a VPS and installed this agent to try it out. My impressions so far:

1. Install experience: since I installed OpenClaw fairly early, when the version was still unstable, I ran into a lot of issues and the setup was tedious. By comparison, this agent is much simpler, supporting a one-click install.
2. Configuration and speed: the GUI-based configuration is very convenient — pair it with a free NVIDIA model, hook up a chatbot, and you're good to go. It's fast, and the documentation is relatively clear.

Logging the whole process here for future reference.

### What is Hermes

Hermes is an open-source AI agent gateway that lets you interact with AI models through Telegram, Discord, WhatsApp, Slack, and other platforms. It's not just a chatbot — it's a genuine agent, capable of running terminal commands, searching the web, generating speech, managing memory, setting up scheduled tasks, and even controlling a browser.

In short, it's an AI assistant that lives on a VPS, reachable anytime through Telegram.

### Installation process

Hermes is installed on my VPS, managed by the `hermes-gateway` systemd service, set to start on boot. Its config file is at `~/.hermes/config.yaml`, environment variables at `~/.hermes/.env`.

On the first launch after install, Telegram responded right away — the basic framework came up without any issues.

### Model configuration

The default install used NVIDIA NIM's `llama-3.1-nemotron-70b-instruct`, but it immediately errored out on startup:

```
Error code: 404 - Function Not found for account
```

The cause: my account didn't have access to that model's NIM Function ID. Switching to `meta/llama-3.3-70b-instruct` fixed it.

I then referenced my model configuration in OpenClaw and added three additional candidate models, all accessed through NVIDIA NIM:

- **Primary model:** `meta/llama-3.3-70b-instruct` — stable, ample free quota
- **Fallback 1:** `minimaxai/minimax-m2.5` — strong Chinese comprehension, good for daily Chinese work
- **Fallback 2:** `z-ai/glm5` — Chinese backup
- **Fallback 3:** `moonshotai/kimi-k2.5` — strong reasoning, the final safety net

All four models share the existing `NVIDIA_API_KEY`, no extra application needed.

### Tools enabled

Hermes manages its tool system per platform — by default, the Telegram platform only has web search turned on, everything else is off. Using `hermes tools enable --platform telegram`, I enabled the rest one by one: **web** (search and content extraction), **terminal** (command execution), **memory** (cross-session memory), **vision** (image recognition and analysis), **tts** (text-to-speech), **skills** (skill extensions), **todo** (task planning), and **cronjob** (scheduled tasks).

### Issues I ran into

**Issue one: web search not working.** After enabling the tool, search requests kept returning "unable to search due to tool restrictions." Turned out Hermes's web_tools only support four search backends — Tavily, Firecrawl, Exa, and Parallel — not Brave Search. The Brave API key I'd previously configured in OpenClaw was completely invalid for Hermes. Ended up registering for Tavily and configuring `TAVILY_API_KEY`, after which web search worked normally.

**Issue two: wrong tool name in config.** I'd manually written `hermes-web` under `platform_toolsets` in `config.yaml`, but the actual tool name is `web`, which kept producing `Unknown tool 'web'` errors in the logs. I later found out the config should be managed through the `hermes tools enable` command, not by editing the config file directly.

### Current status

Hermes is now running stably, with normal Telegram interaction. Next up, I'm planning to configure a daily-briefing cron job to push a morning summary of stocks, oil prices, and exchange rates, along with a daily knowledge practice routine.

As a complement to OpenClaw, Hermes has stronger agent capabilities, especially around terminal execution and multi-tool coordination. The two systems each have their strengths, and for now I'm planning to run them in parallel.
