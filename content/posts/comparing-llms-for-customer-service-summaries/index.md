---
title: "Comparing Open-Source LLMs for Customer-Service Conversation Summaries"
date: 2024-08-03T12:00:04+00:00
summary: "Running ten locally-hosted models on consumer GPU hardware to see which ones best summarize customer-service calls after speech-to-text."
tags: ["LLM", "Ollama", "AI"]
categories: ["Blog"]
featureImage: "https://images.chengyu.eu/file/73cc5b5a8668782b382d9.jpg"
---

### Background

Testing whether locally-hosted, open-source models are good enough to take a customer-service conversation — after it's been converted from speech to text — and produce a usable summary. All of this ran on consumer-grade GPU hardware, since that's what's actually practical for a setup like this.

### Models tested

Ten models pulled via Ollama:

| Model | Size |
|---|---|
| `llama3:70b` | 39 GB |
| `llama3:latest` | 4.7 GB |
| `deepseek-v2:latest` | 8.9 GB |
| `llama3-groq-tool-use:latest` | 4.7 GB |
| `wangshenzhi/gemma2-9b-chinese-chat:latest` | 5.8 GB |
| `glm4:9b` | 5.5 GB |
| `gemma2:latest` | 5.4 GB |
| `gemma2:27b` | 15 GB |
| `qwen2:72b` | 41 GB |
| `qwen2:7b` | 4.4 GB |

### Evaluation approach

Each model was scored across three dimensions: how accurately it captured the customer's underlying intent, how well it documented the steps the agent actually took, and how precisely it identified the core issue. Scores were gathered from a mix of perspectives — customer-service staff, ML practitioners, and end users — and averaged into a final result.

![Evaluation results](https://images.chengyu.eu/file/73cc5b5a8668782b382d9.jpg)
