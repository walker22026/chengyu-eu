---
title: "Trying Out Local LLM Deployment: GPT-3.5 vs. Qwen-7B"
date: 2024-04-23T15:30:00+00:00
summary: "Some hands-on experiments with un-tuned open models, and a few thoughts on where the real value in LLM products actually sits."
tags: ["LLM", "AI", "Self-Hosting"]
categories: ["Blog"]
---

Large language models have always felt a bit abstract to me, so to get a more concrete sense of what they can actually do, I recently deployed GPT-3.5 alongside Alibaba's open-source Qwen-7B locally, and tried applying both to real scenarios from my day job.

### A few test scenarios

**1. Un-tuned open models sitting exams from different professional fields**

I picked a few exams from our internal training platform that don't require any prep beforehand, and used a Chrome AI extension I built myself to have both GPT-3.5 and the open-source Qwen model take them — with no fine-tuning at all. A few things stood out from the results:

- The more specialised the networking knowledge, the better the models scored.
- On national or industry-standard rules and procedures (procurement guidelines, for example), they also scored solidly above average.
- On exams about our own internal, highly customised products, the models fell well short — those questions are simply too specific.

**Takeaway:** an off-the-shelf open model performs roughly like a competent new graduate (arguably a bit better). Some targeted "training" (i.e. fine-tuning) is enough to make it genuinely useful for certain roles.

**2. The "overconfident nonsense" problem**

Careful prompt engineering can mostly stop a model from improvising and keep it answering only from what it actually knows.

**3. How hard is it to stand up a local model?**

The bar is low. Open models like Qwen-7B can be deployed with a single command. There's no real technical difficulty in the deployment itself — what actually needs work is efficiency: GPU driver loading, system-level tuning, that kind of thing.

### Some thoughts

A rough three-layer way to think about LLM architecture:

1. **Foundation layer** — both domestic and overseas open models are already good enough for direct use in a lot of professional contexts. What's needed here isn't teaching a model from scratch, it's the equivalent of upskilling a new graduate. This layer should lean toward optimisation over construction:
   - **Efficiency**: responding quickly to front-end requests and squeezing every bit of GPU capacity out of the hardware.
   - **Fine-tuning**: loading tuning data from different formats into whichever base model sits underneath.
2. **Agent layer** — the underlying model capability is open to everyone (a level playing field), and the front-end requirements are broadly the same across companies. The real differentiation in AI products comes from how well you build the "requirement ↔ model" middleman.
3. **Front-end UI** — presenting the AI capability to the user. This is a comparatively conventional design problem.

![Test results across different exam categories](https://images.chengyu.eu/file/9c6f1d10adf57ed4a4793.png)

### Where this could actually be used

**1. Browser-extension models rolled out department by department**

Using a browser AI extension to vertically uplift existing systems without touching them:

- Guided walkthroughs at the point of service, with root-cause explanations when something errors out.
- Assisted troubleshooting for customer-service staff answering real customer questions.
- Surfacing the exact clause/source behind a procurement rule during the purchasing process.
- Giving analysts a "fine-tuned" explanation of what a given reporting metric actually means.

**2. Building a shared knowledge base across different business lines**

Using an LLM to build out a knowledge base sidesteps two common pain points.

![Illustration](https://images.chengyu.eu/file/11a3967b086dadacfdbe2.jpg)
