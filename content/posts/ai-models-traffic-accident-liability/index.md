---
title: "Head-to-Head: How Mainstream AI Models Judge a Real Traffic Accident"
date: 2026-01-15T18:51:39+00:00
summary: "After my own Tesla was hit by a car pulling out from under a bridge, I ran the accident photo past ChatGPT, Gemini, Grok, Copilot, Claude, and a few domestic models to see which ones actually got liability right."
tags: ["AI", "LLM Comparison"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/01/15/2026-01-15-17.32.31.jpg"
---

### Background

Last Sunday afternoon, while I was driving normally, I collided with a vehicle that pulled out from under a bridge. My first instinct was to just let each side's insurance handle it — mainly because I was worried the process would be slow and complicated. I uploaded photos from the scene through the Tesla app, and when the insurance company called back, they told me clearly: my side wasn't at fault, and recommended letting the traffic police make the official liability determination.

While I was talking with the insurance company, the other driver contacted the traffic police, who told them to come to the local traffic police station to handle it. Once there, the police pulled the surveillance footage from when the accident happened, and their final conclusion was: the other party was fully at fault, responsible for all repair costs.

Since I rarely get into traffic accidents, I wasn't familiar with how liability gets determined. Going back over the whole thing afterward, I found myself wondering: beyond learning the actual traffic-liability rules, are today's large models already capable of making the right call in a situation like this?

### How I tested it

I picked one photo from the accident scene and tested it against several models using the same identical prompt: "Based on Chinese road traffic regulations, and the information in this photo, determine liability for this accident."

![The accident-scene photo used for every model](https://media.chengyu.eu/images/2026/01/15/2026-01-15-17.32.31.jpg)

Here's how the different mainstream models did with this real-world scenario.

### ChatGPT

**Rating: ★**

First up was ChatGPT, the model I use the most day to day. There's no longer a way to pick a specific model — it defaults to ChatGPT 5.2. I'll cut to the conclusion: the result was genuinely disappointing.

The full response was long, so here's just the core of its reasoning: after quoting Chinese traffic law at length, it judged from the photo that the white Tesla was driving against traffic, and on that basis concluded the Tesla bore 100% of the liability. That conclusion was the exact opposite of both the facts and the police's actual determination.

![ChatGPT's response](https://media.chengyu.eu/images/2026/01/15/2026-01-15-17.38.23.jpg)

### Gemini

**Rating: ★★★★★**

Google's Gemini has been moving fast lately — from the TPU rollout to how mature its model applications have gotten, it's started to feel like it's clearly pulling ahead of OpenAI. Apple recently announcing it's bringing in Gemini only reinforced that impression.

In this accident-liability test, Gemini genuinely impressed me — afterward I actually swapped the AI app on my phone's home screen over to Gemini.

Since I only gave it a static photo rather than video, Gemini took a more careful approach and laid out three possible scenarios. In my view, even the first scenario alone was enough to establish that my side wasn't at fault. Working from that, Gemini's liability judgment was basically correct, and its follow-up advice was reasonably sound and professional. (This test used Gemini Pro.)

![Gemini's response](https://media.chengyu.eu/images/2026/01/15/2026-01-15-17.39.53.jpg)

### Grok

**Rating: ★**

This is a model app I've only recently started using — mainly because it's "unrestricted" enough to handle almost any topic or borderline image.

But when it comes to a serious, rules-heavy question like this one, it turned out to be pretty unreliable. In this test, Grok read the scene as a same-direction sideswipe and decided the trailing Tesla bore primary responsibility. I honestly can't figure out how it arrived at "same direction" from the photo.

![Grok's response](https://media.chengyu.eu/images/2026/01/15/39fc87b7ef5793ad1d13c4976460f91a.jpg)

### Microsoft Copilot

**Rating: ★**

I installed Copilot specifically to test this. I'd never used it before, mostly because of a lukewarm impression of it, and this test didn't really change that.

Copilot didn't give a direct judgment at first — it just kept quoting traffic-law provisions. Only after I explicitly demanded "you must give a liability determination" did it actually answer the question.

But then the real problem showed up: it kept getting left and right mixed up — the Toyota was clearly on the Tesla's left, but Copilot repeatedly described it as being on the right, which threw off its whole chain of reasoning. I had to keep cross-checking against the accident photo just to follow what it was saying.

Overall: it wouldn't commit to a conclusion, and when it finally did, the conclusion was clearly wrong.

![Copilot's response](https://media.chengyu.eu/images/2026/01/15/5f4e3a9956ce976ca5b7138c3a13d8d8.jpg)

### Claude

**Rating: ★**

For someone with no development background at all, Claude Code used to feel almost legendary. I once used it myself to whip up an H5 survey form for a business team in just a few minutes.

But for someone who already has some coding ability, that style of interaction doesn't offer much control, so I eventually canceled my subscription and switched to Cursor.

For this test I could only use the free Sonnet 4.5. On the traffic-liability question, Claude's performance fell well short of the impression Claude Code had left me with. It concluded that the Tesla was changing lanes or pulling over and failed to yield to a Toyota going straight — but looking at the actual scene, the two vehicles were nearly at a 90-degree angle to each other, which makes that "lane-change sideswipe" reasoning hard to follow.

![Claude's response](https://media.chengyu.eu/images/2026/01/15/5626de0c21db5464335ca3331ce87ae1.jpg)

### Domestic models

I won't score the domestic models — you can judge the results yourselves.

**Doubao** concluded the Tesla was changing lanes, and so held the Tesla primarily responsible.

![Doubao's response](https://media.chengyu.eu/images/2026/01/15/3ef7130ca66e51e6c20dabbde1b4eb18.jpg)

**Yuanbao** got the right-of-way principle correct, but mixed up which vehicle was on which side.

![Yuanbao's response](https://media.chengyu.eu/images/2026/01/15/d6f926d3e553fee32b4cb50dbbb690c1.jpg)

### OPPO AI

What genuinely impressed me was the AI built into my OPPO phone. Its explanation of the right-of-way rules lined up closely with the traffic police's actual determination — overall, its style was similar to Gemini's: explain the rule clearly first, then give a conclusion, with clean logic and restrained language. That genuinely exceeded my expectations.

![OPPO AI's response](https://media.chengyu.eu/images/2026/01/15/15bcc0f10a2dbac85429474e5d59b642.jpg)

### Final thoughts

I'm not an AI expert, and none of the above is meant as a rigorous benchmark — just one regular, heavily-AI-dependent user's comparison in a real situation.

Going through the process also gave me a real appreciation for how digitized the traffic police system has become here: after the report was filed, officers could pull up the scene footage directly, complete a contactless review using a visualization system, and once liability was determined, the ruling synced straight to the official traffic-management app.

![The official liability determination synced to the traffic app](https://media.chengyu.eu/images/2026/01/15/9f7a5b64a6ea8db603b35f40e486efff.jpg)

As long as there's no dispute, the whole process is efficient, clear, and traceable.

One more thing that stuck with me: if what had pulled out from under that bridge hadn't been a car, but an e-bike, or a pedestrian, the nature of the whole incident — and its consequences — would have been completely different. Even with liability clearly established, it could easily have turned into a personal-injury case, with the cost and stress multiplying many times over. This accident was a reminder, once again, that safe driving is what actually matters most.

![One more look at the scene](https://media.chengyu.eu/images/2026/01/15/822f60afa1f1780a76744045b4efb47a.jpg)
