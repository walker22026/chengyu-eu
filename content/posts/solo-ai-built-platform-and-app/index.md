---
title: "\"Code Is Dead\"? What Building a Platform and App Solo With AI Actually Taught Me"
date: 2026-02-08T12:00:11+00:00
summary: "Codex framed the app, Cursor did the surgical edits, and a working SIP call-center app got built by one person — the rough UI is the actual lesson here."
tags: ["AI", "Coding"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/02/08/Pasted-image-20260208114153.jpg"
---

NVIDIA CEO Jensen Huang's recent comment — "writing software code is dead" — sent a shockwave through the tech world. A lot of people found it alarming, but honestly, I agree with it. That's not to say we can throw our computers away — it means the barrier to the actual act of "writing code" is dropping toward zero. The real competitive edge has shifted from "knowing the syntax" to "being able to turn domain expertise into a product."

Whoever can use AI well enough to quickly turn the specialized knowledge in their own head into a real product is the one who wins in this new era.

I've recently been building exactly that way — single-handedly putting together a complete platform plus a companion app. Here's what that process actually taught me, especially around which AI tools to use and how I've rethought what "domain expertise" even means now.

### Building it: Codex for the frame, Cursor for the fine work

On this project, I built both the platform side and the mobile app on my own — work that used to take a small dev team several weeks.

My main AI toolkit was **Codex plus Cursor**.

**Codex: the bold builder.** Early on, Codex's strong generation ability let me stand up the underlying code framework fast. Give it a clear instruction and it lays the foundation and puts up the walls almost instantly.

**Cursor: the precise scalpel.** As the codebase grew, though, relying purely on Codex's "generate a big chunk" style started causing problems — once the project got more complex and I needed tighter control over changes, Codex would occasionally hallucinate or break existing logic. That's where Cursor became indispensable — more like an experienced engineer: quickly locating the exact snippet that needs changing, proposing a fix rather than just overwriting things outright, and leaving me with final say before anything actually changes.

That combination — broad strokes for the big structure, precise edits for the fine detail — let one person handle genuinely complex business logic.

![The app's call interface](https://media.chengyu.eu/images/2026/02/08/Pasted-image-20260208114153.jpg)

### The result: a one-person army

Through this workflow, I built **KanjiGo** — the app and its backend system. Even built solo, the core functionality is genuinely solid, with a complete, fairly involved communication stack: SIP calling built into the app's front end (the call interface shown above), smart routing on the backend that assigns calls to different agents, and a queuing strategy that handles priority when agents are busy.

From the low-level protocol up through the front-end interaction, everything is fully functional — work that used to require a backend developer who understood VoIP protocols, a mobile developer, and a UI designer all working together.

### The "rough" UI is actually the best proof of Huang's point

At this point someone might reasonably say the app's UI looks a bit rough. Fair — it's not polished. But that's exactly the part I want to highlight, and it's the best evidence for Huang's argument.

Why does the UI look rough? Because I lack domain expertise in design. AI is powerful enough to write me flawless calling logic, because I understand the technical logic and can direct it. But I don't have much of an eye for design — I don't know what "good design" actually looks like, so I can't give the AI prompts with real aesthetic judgment behind them. No matter how capable the AI is, it can't conjure taste it's never been shown.

What does that tell us? AI is an amplifier. If you understand the technical logic, AI lets you do the work of an entire dev team by yourself. If you understand visual design, AI lets you produce master-level UI fast. What I'm missing right now is just someone with real design experience handing me a proper design spec — with that document in hand, capturing real design expertise, I'm fully confident I could direct the AI to take this rough interface and turn it into a top-tier visual experience almost instantly.

### Closing thought

Software development isn't dead — it's just taking a different shape. Tomorrow's developer doesn't need to memorize every API by heart — they need to become a product manager with real depth in their field, and an architect who knows how to direct AI.

As long as you have a genuine, deep understanding of some domain — communications, healthcare, education, art, whatever it is — and know how to ask AI the right questions, you can build things that used to be unimaginable. A rough interface isn't the real problem — that's just technique. Complete functionality and coherent logic — that's the substance.

Embracing AI isn't just about writing code. It's about unlocking the real value of what you already know.
