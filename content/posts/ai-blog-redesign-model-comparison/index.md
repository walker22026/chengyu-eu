---
title: "When AI Starts Thinking Like a Designer: Comparing Gemini, Claude Opus, and GPT on a Real Redesign"
date: 2026-01-07T12:00:14+00:00
summary: "Three models, one vague brief to redesign a blog's UX, visuals, and SEO — and a clear split between models that plan first and models that just ship a result."
tags: ["AI"]
categories: ["Blog"]
---

Recently I ran an experiment that was interesting, and pretty practical: having different large models redesign one of my blog sites, using a method as close as possible to how I'd actually work day to day.

This wasn't about benchmarking models or showing off prompt-engineering tricks — it came from a very plain question: a blog page that doesn't look great, has mediocre UX, and isn't great for SEO either — can AI help me improve the whole thing?

What I actually cared about was this: among the models now being called "next-generation," which one behaves more like a competent colleague when handed something real, ambiguous, and goal-oriented but without a prescribed solution — rather than just a tool.

### 1. The testing approach: brief it like you'd brief a colleague

In real work, we rarely describe a design problem in precise technical language. More often we say things like: "this page feels a bit messy," "the structure isn't very clear," "it doesn't look very premium," or "it's not great for search engines."

So for this test I deliberately avoided writing an elaborate prompt, and used only three very natural lines describing what I wanted, asking each model to redesign the page across three dimensions: user experience (UX), visuals, and SEO. No framework specified, no style specified, no task breakdown — just seeing whether the model could figure out on its own what to do, and in what order.

### 2. The models compared

Three models went head to head: Gemini 3 (Google), Claude Opus 4.5 (Anthropic), and GPT-5.1 Codex (OpenAI). The test conditions were identical across all three: the same starting page, the same description, and no model-specific prompt tuning at all.

### 3. Results: a clear split in design ability

The conclusion from the final results was pretty clear: if you're judging overall design ability, Claude Opus 4.5 came out ahead.

"Design" here isn't just "does it look nice" — it's a more complete idea covering things like whether the page structure makes sense, whether the information hierarchy is clear, whether the user's path through the page flows well, whether it proactively considered technical SEO, and whether it filled in important design considerations I hadn't explicitly asked for. Opus 4.5 was noticeably more complete on all of these fronts.

### 4. The key difference: not generation ability, but planning depth

What struck me most from this comparison is that the real gap between these models isn't about whether they can generate something anymore — it's about whether they think it through before generating.

**Gemini 3 leaned toward getting to a result quickly.** Its output wasn't wrong, exactly, but overall it felt more like it jumped straight to a solution, without much explicit reasoning about the overall structure first — the design decisions felt a bit scattered. It came across more as "figuring it out while building" than "designing, then building."

**GPT-5.1 Codex was strong on engineering execution.** Its performance was solid — a clear technical path, reliable implementation logic, and well suited to actually turning a plan into working code. But in this test it acted more like a highly capable front-end engineer than something driving the overall design direction.

**Opus 4.5 planned first, then built it out.** The biggest difference with Opus 4.5 was that it laid out a complete design rationale up front, made the relationship between UX, content structure, and SEO explicit, and only then worked step by step toward the concrete implementation. It also proactively handled a number of things I hadn't explicitly asked for, but that any real design work would need to account for. That's what set the ceiling on the final result's quality.

### 5. What actually impressed me was the shift in how the work got done

Judging purely on output quality, this was already a successful test. But what really stood out to me was the change in the entire workflow: in under 20 minutes, working on the same site, I ended up with three complete, deployable design-and-optimization proposals — and the only thing left for me to do was pick the one I liked best.

In a traditional process, it's hard to get a colleague to produce multiple complete proposals that cover design, structure, and SEO all at once, in that short a time, without a lot of back-and-forth.

### 6. A practical takeaway from this comparison

This test left me more convinced of one thing: AI is shifting from being a "tool" to something closer to a collaborator with an actual role to play — but only if you hand it a role-level task rather than an instruction-level command.

In practice, I've now settled into a rough division of labor: use Opus for ideation, planning, and design direction; use Codex for engineering implementation and shipping code; use Gemini for quickly validating an idea. Rather than agonizing over which model is "best overall."

### 7. Closing thoughts

For this blog redesign, I've already gone ahead and shipped one of the three proposals. Not because it was perfect, but because it was extremely low-cost, highly efficient, clearly reasoned, and professional enough.

If AI used to be mostly about helping you get work done, it's now starting to participate in the judgment calls and the design decisions themselves. That might be the part of this next generation of models that's actually worth paying attention to.
