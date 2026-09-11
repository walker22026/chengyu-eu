---
title: "Today's Ops Log: Switching AI Tools and Reworking the Network"
date: 2026-01-23T15:00:18+00:00
summary: "Moving from Cursor to OpenAI Codex, two days spent simplifying the network layout, a fix for Podsync's YouTube rate-limiting, and a disposable-container trick for benchmarking a NAS."
tags: ["Self-Hosting", "AI", "Tools"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/01/23/Pasted-image-20260123144915.jpg"
---

### 1. Dev environment: moving from Cursor to OpenAI Codex

I've stopped using Cursor and switched to OpenAI Codex, mainly for cost reasons — it piggybacks on my existing ChatGPT Plus subscription ($20/month), so there's no separate Cursor subscription to pay for on top.

Looking back on Cursor: the experience was genuinely excellent and it made me noticeably more productive — I recently used it to build an Android SIP communication app — and along the way I picked up a decent feel for using an AI coding assistant well, which should carry over to Codex.

![Setting up Codex](https://media.chengyu.eu/images/2026/01/23/Pasted-image-20260123144915.jpg)

A few notes on getting Codex set up: you can use it through the web, directly in the ChatGPT Plus interface by selecting the Codex tool; through the CLI, installed with `npm install -g @openai/codex` for direct terminal interaction; or as an IDE plugin, installed and authorized inside VS Code. Usage is included in the ChatGPT Plus subscription and shares that plan's usage limits (officially described as supporting multiple focused coding sessions per week).

### 2. Reworking the network setup

Spent a full two days doing a complete overhaul of the existing network architecture. The key change: dropping the layered Caddy setup in favor of a simpler network path.

### 3. Fixing Podsync's YouTube rate-limiting

The original setup kept tripping YouTube's anti-scraping/rate-limiting warnings. I worked out a fix that avoids the rate-limiting issue while still pulling audio/video without burning a lot of bandwidth on unnecessary cloud-storage syncing, and put together a small `curl` script to check ahead of time whether a given video is likely to trigger rate-limiting:

```
curl -sS -A "Mozilla/5.0" \
  -H "Content-Type: application/json" \
  "https://www.youtube.com/youtubei/v1/player?key=xxxxxx" \
  --data '{"videoId":"q1WpRru-OA8","context":{"client":{"clientName":"WEB","clientVersion":"2.20240101"}}}' \
| jq -c '.playabilityStatus | {status: .status, reason: .reason, subreason: .messages, errorScreen: (.errorScreen.playerErrorMessageRenderer.subreason.runs[0].text // empty)}'
```

### 4. A NAS trick: benchmarking inside a disposable container

The pain point: NAS systems (Synology, for example) often don't have a normal environment to run a network-speed benchmark in. The fix: run a one-off Docker container just to do the benchmark.

The idea is simple — host machine starts a container, the script runs inside it, then the container exits and gets torn down automatically. In practice:

Start a temporary container:

```
docker run --rm -it debian:12 bash
```

Inside the container, install what's needed and run the benchmark:

```
apt update && apt install -y wget curl procps && wget -qO- bench.sh | bash
```

Then just `exit` — the container cleans itself up automatically.

Or, as a one-liner that does the whole thing at once:

```
docker run --rm -it debian:12 bash -c "apt update && apt install -y wget curl && wget -qO- bench.sh | bash"
```
