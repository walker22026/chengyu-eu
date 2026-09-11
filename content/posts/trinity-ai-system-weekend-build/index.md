---
title: "This Weekend I Finally Built the Three-Part AI System I'd Been Thinking About"
date: 2026-04-20T02:01:20+00:00
summary: "A personal assistant on the home NAS, a home assistant tied into an investment system, and an intelligence-gathering agent on a VPS — three modules starting to work together as a real perception-cognition-action loop, not just a pile of tools."
tags: ["Self-Hosting", "AI"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/04/19/Pasted-image-202604200002571c32ee74b107e7fa.jpg"
---

This weekend, I finally built out a piece of AI architecture I'd been mulling over in my head for a long time.

It's not the kind of thing where you just "bolt on a few tools" — it's starting to feel like an actual system. Three modules, each with its own role, and starting to show a bit of real coordination between them.

### The first piece is a personal assistant

For this one, I basically carried over the Hermes agent I'd already been tinkering with.

As I've written before, I deployed it on my Synology NAS so it stays online long-term, handling information gathering and processing. Compared to a cloud-based setup, this kind of "locally resident" feel is more like a truly personal assistant — stable, controllable, and something you can slowly feed data over time.

Next, I plan to gradually feed my own past journal entries into it, so it genuinely understands my behavior patterns and decision logic, rather than just being a "tool-style AI."

I want it to become more of a "long-term memory + cognitive augmentation" role going forward.

![Trinity AI architecture sketch](https://media.chengyu.eu/images/2026/04/19/Pasted-image-202604200003274b7106f0e42ea988.jpg)

### The second piece is a home assistant

I built this one myself, from scratch.

From the architecture down to the features, it's mostly built around "voice understanding + intent recognition." Put simply, the goal is for it to understand what I'm saying and know what I want it to do.

The single most important thing right now is connecting it to my investment system.

That includes position information, rebalancing alerts, strategy triggers — gradually giving it access to all of this. For example: when the market moves, it can alert me; when a strategy condition is hit, it can notify me or even act; for day-to-day use, it can also give simple status summaries.

This piece is really the "most action-oriented" part of the whole system. It's not focused on "thinking" the way the personal assistant is, and it's not focused on "input" the way the intelligence-gathering system is — it's closer to an "action layer."

### The third piece is an intelligence-gathering agent, handled by OpenClaw

I'm running this module on a VPS. The reason is simple: speed and network access.

Its role is very clearly defined — "intelligence gathering": quickly pulling in information, catching key changes, sending alerts as soon as something happens. No complex analysis, no long-term memory — just a "forward outpost."

OpenClaw already had the rough shape of this before; this is really a repositioning, upgrading it from a tool into the "perception layer" of the whole architecture.

### Putting it together

With the three pieces put together, for the first time it feels less like a handful of tools and more like a real system: the intelligence agent on the VPS handles "seeing the world," the personal assistant on the NAS handles "understanding me," and the local home assistant handles "acting on my behalf." Something like a simplified "perceive–think–act" loop.

Of course, right now it's only at the stage of "it runs." So next, I'm going to let it run for a while rather than rushing to add new features. Watching first for which parts are actually delivering value, which alerts are just noise, and which capabilities are worth turning into long-term, stable mechanisms. Once it's running smoothly, I'll think about the next round of tuning and upgrades.
