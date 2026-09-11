---
title: "LLM Agents 101"
date: 2024-07-30T00:00:09+00:00
summary: "What an 'agent' actually means in the context of large language models, and a worked example in a customer-service setting."
tags: ["LLM", "AI", "Agents"]
categories: ["Blog"]
---

### What is an agent?

In the context of large language models, an "agent" is an LLM-based system that can autonomously carry out tasks, make decisions, and interact with its environment. It combines the reasoning power of an LLM with the autonomy of a traditional software agent. A few defining traits:

1. **Autonomy** — an agent can understand a task, plan, and act on it without constant human intervention.
2. **Goal-directed** — it's given a specific objective and works toward it.
3. **Environment interaction** — it can perceive its surroundings and adjust its behaviour as conditions change.
4. **Reasoning and decision-making** — it uses the underlying LLM's capabilities to reason through non-trivial decisions.
5. **Tool use** — many agents are built to call out to external tools and APIs: search engines, databases, or other software.
6. **Continuous learning** — some more advanced agents can learn and improve from experience.
7. **Multimodality** — some agents can process and generate more than just text, including images and audio.

### Common agent frameworks

A quick comparison of a few frameworks I looked at, roughly by strengths and how steep the learning curve is: LangChain, AutoGPT, Hugging Face Transformers, NVIDIA NeMo, and Microsoft's DeepSpeed Chat — each has a different sweet spot depending on whether you're optimising for flexibility, ease of setup, or production performance.

### A worked example: telecom customer service

As a concrete example, I sketched out a customer-service agent for a telecom scenario: it takes speech input, converts it to text, looks up account information (like balance) in a database, generates a response, and converts that back to speech. One deliberate design choice worth calling out: if the model's answer doesn't explicitly mention the account balance, the system appends it automatically — partly for compliance and consistency, and partly so the answer is useful even if the model's own response wanders off-topic.
