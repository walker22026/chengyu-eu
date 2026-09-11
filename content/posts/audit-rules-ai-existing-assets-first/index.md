---
title: "Build From What Already Exists, Then Generate: An AI Audit-Rules Project"
date: 2026-04-18T10:01:41+00:00
summary: "Writing the plan for an AI-generated audit-rules project, we chose to reverse-engineer rules from years of proven production scripts rather than rebuilding a rule system from raw data and business logic."
tags: ["AI"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/04/17/-1.jpg"
---

I've recently been in charge of writing the project plan for AI-generated audit rules.

When working on the AI plan for audit rules, we initially considered working our way up from the underlying data and business rules step by step, but quickly realized that path was too inefficient, with an unpredictable timeline.

Instead, we leaned toward putting the assets already accumulated in our existing audit system to use — reverse-generating audit rules and dictionary libraries via scripts, effectively building the AI capability on top of what already exists rather than rebuilding the whole system from zero.

We put the focus of phase one on "existing stock" rather than "incremental growth." The core reason: the existing audit scripts are themselves the product of long-term production validation — mature business logic, high reliability, making them the highest-quality training data available. At the same time, whether it's the group-level revenue assurance system, cBSS auditing, or provincial custom auditing, these three systems together already cover the vast majority of mainstream scenarios — there's no "not enough data" problem in the near term.

We're prioritizing the path "script → AI parsing → human confirmation → ingestion," using SQL scripts already validated in production as the core input, letting the AI understand and structure these rules, with humans reviewing before anything gets finalized into a standardized knowledge base. Only once this foundation is solid will we gradually consider opening up the ability to generate rules directly from natural language — but always on the condition that results stay controllable, auditable, and reversible.

Prioritizing structured consolidation of existing assets this way lets us quickly stand up a usable AI capability on one hand, while continuously refining and iterating the model based on real business feedback on the other. In essence, we're using "already-validated knowledge" to constrain the AI's output boundaries, structurally avoiding the "hallucination" problem that comes with fully free-form generation. In other words, we're not having the AI invent rules from scratch — we're having it understand, abstract, and enhance within an existing rule system, which preserves both efficiency and a quality floor.
