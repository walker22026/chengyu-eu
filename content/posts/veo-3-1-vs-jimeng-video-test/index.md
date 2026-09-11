---
title: "Veo 3.1 vs. Jimeng: Stress-Testing an Image-to-Video AI Workflow"
date: 2025-12-18T18:02:01+00:00
summary: "The same prompt, two video models — one produced a slightly rough transition clip, the other gave a character a 180-degree head spin."
tags: ["AI"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2025/12/18/E.jpg"
---

Today I tried out a new AI video workflow: first generate a set-environment image with an image-generation tool, then use **Veo 3.1**'s image-to-video bridge feature to generate a transition clip between two photos.

![One of the generated transition frames](https://media.chengyu.eu/images/2025/12/18/E.jpg)

Honestly, the final result still has room to improve on smoothness. The main reason is that Veo 3.1's generation quota is pretty limited, so I couldn't do repeated fine-tuning or re-rolls — I just had to accept a transition that came out a little rough around the edges.

![Another frame from the Veo 3.1 result](https://media.chengyu.eu/images/2025/12/18/D.jpg)

That said, there's no appreciation without comparison. Out of curiosity, I ran the exact same prompt through **Jimeng (即梦)**, and the result nearly gave me a heart attack — the character's head did a full 180-degree spin in the generated video. For a second it felt like something out of a horror movie; watching that late at night genuinely startled me.

![The considerably less reassuring Jimeng result](https://media.chengyu.eu/images/2025/12/18/C.jpg)

**Takeaway:** at this point, Veo 3.1 is clearly more reliable than Jimeng when it comes to understanding physics and human anatomy. It burns through GPU time and quota fast, but at least it doesn't turn your film set into a horror movie.
