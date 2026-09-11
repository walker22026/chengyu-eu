---
title: "Deleting My Fine-Tuning Notes: From 'Taming a Model' to 'Steering a Process'"
date: 2026-02-08T03:00:07+00:00
summary: "Clearing out a year-old Obsidian folder on model fine-tuning made the shift obvious: from chasing model capability to chasing business results through workflow and RAG."
tags: ["AI", "LLM"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/02/07/Pasted-image-20260207225934.jpg"
---

Today I gave my Obsidian vault a real cleanup — clearing out a batch of notes that started out red-hot and then just stopped cold. Looking back at these half-finished threads felt less like tidying and more like watching my own thinking evolve. The most obvious one was a series from April 2024 on large-model fine-tuning.

Back then, I was genuinely eager to "train" a model with domain expertise.

![A look back at the old fine-tuning notes](https://media.chengyu.eu/images/2026/02/07/Pasted-image-20260207225934.jpg)

### 1. The 2024 conviction: building a "digital colleague"

In a note from April 23, 2024, I'd sketched out a pretty simple vision: I wanted the model to behave like a fresh graduate on their first day — strong general ability, but no industry context or business logic yet. Back then I was convinced fine-tuning was the only path to actually getting it up to speed — injecting domain knowledge and rules to turn a general model into an industry-specific one.

Chasing that goal, I set up a dedicated machine with an NVIDIA card on April 27th, and by May 8th, after working through environment setup, VRAM limits, and data cleaning, I finally had the model running locally. That feeling of "mastering the model" felt, at the time, like the finish line for the whole effort.

### 2. The shift: fine-tuning stopped being the default path

Looking back now, though, the model ecosystem moved faster than anyone expected. Re-reading these notes today, I can see my core assumptions have shifted structurally. General-purpose models have gotten close to expert-level even in code, reasoning, and specific industry depth, so the number of situations that genuinely require fine-tuning has shrunk fast. And as RAG (retrieval-augmented generation), agents, and workflows have matured, it's become clear that a lot of the time the model's capability wasn't the bottleneck — how we organized the data and the task was.

Fine-tuning went from being everyone's default starting point to a heavier, more specialized tool you reach for only in particular situations.

### 3. From "capability" to "value": where the focus moved

I deleted these notes because they no longer reflect how I think now. Going through them made the shift over the past year really clear:

| | **Then (early 2024)** | **Now** |
| --- | --- | --- |
| Main focus | The model itself (parameters, training, fine-tuning) | Applying the model (agents, automation, systems) |
| Perspective | Experimental: how do I train the model well? | Engineering: how do I get the model to keep producing value? |
| Approach | Compute + data + fine-tuning | Prompting + workflow + RAG |
| End goal | Chasing model capability | Chasing business results |

I used to care about operating on the model's "brain." Now I care more about giving it a good toolbox and a clear standard operating procedure to work from.

### Closing thought

Those deleted notes are really a marker of one stage of exploration ending. A technical path is never a straight line — it's a constant process of revising your own earlier conclusions. In the AI era, some experiments leave behind code and models; for others, the process itself is the real output.

Rather than staying fixated on taming the perfect model, it's more worthwhile to build the system that lets the model actually shine.
