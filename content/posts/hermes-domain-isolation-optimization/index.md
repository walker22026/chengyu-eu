---
title: "How I Rebuilt My Hermes Assistant So My Life's Domains Stop Bleeding Into Each Other"
date: 2026-04-29T10:00:06+00:00
summary: "Investing talk kept leaking into work notes and vice versa — the fix was splitting Hermes into a router plus dedicated stock, work, diary, and life sub-assistants, each with its own knowledge base partition and a strict no-cross-referencing prompt."
tags: ["AI", "Self-Hosting"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/04/28/Pasted-image-20260429085852.jpg"
---

In everyday life, we touch on a number of different domains — investing, work, journaling, day-to-day life, and so on. As AI assistants have matured, something like Hermes has increasingly become a genuine helper. But the deeper I got into using it, the more I noticed a real problem: despite its strong capabilities, the domains kept bleeding into each other.

Talking about stocks would often drag in bits of my work content, and journaling about daily life would occasionally pull in investment thinking. That kind of cross-contamination got me thinking about how to properly separate different tasks and content domains so Hermes could serve me more efficiently and professionally in each one.

### 1. The core principle: one entry point, multiple isolated roles

First, I decided to restructure Hermes as a single main entry point backed by several specialized sub-assistants. Specifically, I split Hermes into five modules:

- **Router Hermes:** determines which domain the current task belongs to and hands it off to the right sub-assistant.
- **Stock assistant:** focused on stock analysis, asset allocation, long-term holdings, and related questions.
- **Work assistant:** covers the audit system, customer-service work, project reporting, AI-enablement initiatives, and similar content.
- **Diary assistant:** records personal reflection, blog content, tech tinkering, and so on.
- **Life assistant:** handles family, health, travel, Portuguese learning, and other everyday matters.

Each domain module needs a strict boundary against the others, so content doesn't cross over and each module stays focused only on tasks within its own domain.

### 2. The most important piece: partitioning the knowledge base

To make domain isolation actually work, I gave Hermes's knowledge base a clear partition structure. Content for each domain lives in its own directory, for example:

```
knowledge/
├── investment/
│   ├── portfolio.md
│   ├── stock_notes.md
│   ├── strategy.md
│   └── risk_rules.md
├── work/
│   ├── audit_system.md
│   ├── customer_service_ai.md
│   ├── cbss_billing.md
│   └── reports.md
├── diary/
│   ├── personal_journal.md
│   ├── blog_drafts.md
│   └── reflections.md
├── life/
│   ├── family.md
│   ├── health.md
│   ├── travel.md
│   └── portuguese.md
```

When I ask Hermes something, it first determines which domain the question belongs to, then only retrieves material from that domain — avoiding cross-domain interference.

### 3. Adding an "intent routing" layer

On top of the knowledge-base partitioning, I added an "intent routing" system. This means that every time I ask a question, Hermes first determines which domain it falls under, then decides whether cross-domain reference material is even allowed.

For example: if I ask "How did the stock market do today?" Hermes only pulls stock-related material. If I ask "How's the AI-enablement project at work going?" Hermes only touches work-domain content. If I ask "Help me plan a trip," Hermes stays within the life domain and leaves investing or work out of it entirely.

This routing mechanism effectively ensures content from one domain doesn't get misapplied to another.

### 4. Per-module role prompts

Each domain module has its own dedicated role prompt, defining how it should behave when answering. For instance:

**Stock assistant:**

> You are my stock-analysis assistant.
>
> Responsibilities: analyze stocks, ETFs, asset allocation, long-term holdings, rebalancing suggestions, and related questions. Focus on investment decisions around steady growth, retirement funds, and margin of safety. Prioritize stock-related material when answering.
>
> Off-limits: don't proactively reference my work content; don't proactively reference my family, diary, or Portuguese-learning content.

**Work assistant:**

> You are my work-material and project-analysis assistant.
>
> Responsibilities: help organize reporting material, project summaries, the audit system, customer-service tickets, AI applications, and similar work content. Tone should be concise and formal, suited for reporting to leadership, highlighting goals, actions, and results.
>
> Off-limits: don't proactively reference my investment content; don't proactively reference my family or diary content.

**Diary assistant:**

> You are my personal diary and blog-writing assistant.
>
> Responsibilities: organize personal experiences, tech tinkering, AI tool usage, and life observations. Voice should be first-person, conversational, with personal opinion.
>
> Off-limits: don't proactively expose specific work content; don't fold personal life experiences into work material.

**Life assistant:**

> You are my life-planning assistant.
>
> Responsibilities: handle family, health, travel, foreign-language learning, and other everyday matters. Answers should be practical, concrete, and actionable.
>
> Off-limits: don't proactively pull in work content; don't proactively reference investment positions unless it's directly relevant to financial planning.

Each module sticks strictly to its own defined scope when answering, staying on topic.

### 5. Combining hard isolation with soft isolation

To make the domain separation even more reliable, I used two approaches together: **hard isolation**, meaning each domain has its own independent knowledge base so content never gets confused across domains; and **soft isolation**, using intent routing and role prompts to keep each module operating strictly within its scope, with Hermes only blending domains when I explicitly ask for a cross-domain answer.

### 6. How the router is designed

The router Hermes's main job is to determine a question's domain and dispatch it to the right assistant. For example: "How did the stock market do today?" → routed to the stock assistant. "Help me write a report on the AI-driven collections system" → routed to the work assistant.

This "router + specialized sub-assistants" design lets Hermes operate efficiently across multiple domains while avoiding interference between them.

### 7. Wrap-up

By restructuring Hermes with clear domain isolation, intent routing, and modular roles, I finally arrived at an assistant that actually works efficiently. Whether it's stock analysis, work reporting, or personal journaling and life planning, Hermes now helps me within clearly defined boundaries. This hasn't just made Hermes more efficient — it's made it a genuinely capable helper across both my daily life and my work, with information from different domains no longer stepping on each other.
