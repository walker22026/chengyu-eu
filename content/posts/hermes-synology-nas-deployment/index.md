---
title: "Migrating Hermes to My Synology NAS"
date: 2026-04-19T22:00:12+00:00
summary: "After getting Hermes running on a VPS, I moved it to my Synology NAS instead so it could gradually gain access to household journal and investment data as an internal AI hub — and redesigned the model routing along the way."
tags: ["Self-Hosting", "AI"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/04/19/Pasted-image-20260419211357.jpg"
---

Got Hermes fully working on the VPS yesterday, and used it today — overall it feels better than OpenClaw: the information is more concise, and the config files are much clearer. I'm considering making it a service on my home network, with the goal of gradually turning it into the AI channel that handles interaction with core internal content like my investment platform and household journal. So I decided to migrate Hermes from the VPS to my Synology NAS, and took the opportunity to redesign the model routing strategy from scratch.

### Why migrate to the NAS

After migrating to the NAS, Hermes runs via Docker still confined to my home network, but through gradual authorization it can progressively gain access to my household journal, investment records, and other information, becoming an internal AI hub. The end result: outbound information gathering handled by the VPS-based OpenClaw architecture, while internal content and investment-related work goes through Hermes on the internal Docker setup — a cleaner architecture overall. The VPS instance of Hermes has been stopped and disabled.

### Deployment process

Deployed via Docker Compose on a DS218plus — the config is very lean.

### Default model configuration

Hermes accesses all models through a LiteLLM gateway, with the config pointing at a single unified endpoint, with the core goal still being the lowest possible token cost.

### Attempting smart routing for short messages

With the primary model configured, the next step was further cost optimization — short messages don't need to go through the primary model; a cheaper model would do. Hermes offers a `smart_model_routing` feature for this.

### Why the routing didn't work

Testing showed the routing wasn't behaving as expected. Sending a short Chinese sentence like "今天是星期日" (Today is Sunday) still came back through Gemini Flash. Only an extremely short English message like "hi" actually triggered MiniMax. Checking the logs revealed why.

### Giving up on routing, switching to a full fallback chain

Since smart routing was essentially ineffective for Chinese input, and forcing traffic to a cheaper model risked hurting response quality, I ultimately decided to drop the routing approach and build out a complete fallback chain instead.

### A hidden problem in the config

`config.yaml` had two `smart_model_routing` blocks — one at the top that I'd manually added (`enabled: true`), and one in the middle from the default template (`enabled: false`). When parsed as YAML, the later block overrode the earlier one, so smart routing was actually disabled the whole time.

### Final state

Hermes on the NAS is now running stably, with the VPS instance stopped and its autostart disabled. The model setup uses Gemini Flash as the primary, with multiple fallback layers for reliability, and Sonnet 4.6 available on-demand for top-tier reasoning.
