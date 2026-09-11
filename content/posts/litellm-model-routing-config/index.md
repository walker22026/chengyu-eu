---
title: "Getting LiteLLM's Model Routing Set Up Properly"
date: 2026-04-19T23:00:11+00:00
summary: "A full writeup of the LiteLLM gateway config behind my AI stack — free NVIDIA NIM and OpenRouter models as the safety net, cheap paid models like DeepSeek V3 for daily use, and Claude Sonnet as an on-demand top-tier fallback."
tags: ["Self-Hosting", "AI"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/04/19/Pasted-image-202604192114170aaa6e8ec592c62a.jpg"
---

Spent some time today thoroughly reworking the LiteLLM gateway's model routing strategy — writing down the full thinking and the final config here.

### Why model routing is needed

My AI infrastructure looks like this:

```
Telegram → Hermes → LiteLLM gateway → various models
```

LiteLLM acts as a unified gateway, exposing a single OpenAI-compatible interface on top of a dozen-plus backend models. Hermes only needs to know one address, `ai.XXX.com` — it doesn't need to care which model is actually running underneath. The benefit: models can be swapped, upgraded, or switched at any time, completely transparent to Hermes.

### The model pool

The final configuration:

**Paid models (pay-as-you-go, very low cost)**
- DeepSeek V3 — strongest for Chinese, $0.27/million input tokens, extremely cost-effective
- Gemini 3 Flash — Google's latest fast model, multimodal, generous free quota
- Gemini 3.1 Pro — strong reasoning, used as a Pro-tier backup during rate limits

**Free NVIDIA NIM models**
- `meta/llama-3.3-70b-instruct` — general-purpose English workhorse
- `minimaxai/minimax-m2.5` — strong Chinese ability
- `moonshotai/kimi-k2.5` — strong reasoning
- `z-ai/glm5` — Chinese backup (later swapped for the OpenRouter version due to timeouts)

**OpenRouter free model pool (final safety net)**
- `minimax/minimax-m2.5:free`
- `z-ai/glm-4.5-air:free`
- `meta-llama/llama-3.3-70b-instruct:free`
- `nvidia/nemotron-3-super-120b-a12b:free`

**On-demand paid top-tier model**
- Claude Sonnet 4.6 — accessed through OpenRouter, $3/$15 per million tokens, top-tier reasoning backup

### The final fallback chain

Whenever any model fails, there's a complete degradation chain in place, with a final round-robin pool of four free models as the ultimate safety net.
