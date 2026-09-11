---
title: "Building an Automatic Podcast Transcription and AI-Organizing System on My Synology NAS"
date: 2026-08-18T18:00:12+00:00
summary: "Rather than trying to run Whisper locally on an underpowered NAS, I offloaded transcription to Cloudflare Workers AI and article cleanup to LiteLLM, turning the NAS into an automation hub with its own web player for listening, searching, and reading."
tags: ["Self-Hosting", "AI"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/08/18/Pasted-image-20260818171608.jpg"
---

I recently tinkered with something else on my Synology NAS.

I'd already been using Podsync to auto-download a bunch of podcast shows, with new audio coming in pretty much every day — mostly Chinese, with some English and Portuguese shows mixed in.

Once the audio's downloaded, I can obviously just listen to it directly — but I gradually noticed a problem. With podcasts, especially shows with a lot of back-and-forth between multiple speakers, there's a fair amount of filler, and it's not easy to quickly zero in on the actual content you need from the audio alone.

So I started thinking: since these shows are already getting auto-downloaded to the NAS, why not take it one step further — **once the audio's downloaded, automatically transcribe it to text, then have AI clean it up into something more readable.**

That way, a given episode could be listened to, read, searched, and kept long-term, all at once.

So I started tinkering.

### First idea: just run Whisper directly on the NAS

My first thought was actually pretty simple: since the files are already on the NAS, just deploy Whisper there and transcribe locally right after download.

But after checking my Synology's specs, I dropped that idea quickly. Mine is a DS218+, with an Intel Celeron J3355, dual-core. The RAM had already been expanded to about 10GB, but there's no dedicated GPU, and the CPU doesn't even support AVX/AVX2.

A small model isn't entirely out of the question. But being able to run it and being suited to running it long-term are two completely different things.

My Podsync setup pulls in roughly two hours of new audio every day. Using a tiny model for speed means accuracy suffers too much; using a bigger model means the NAS's processing speed probably can't keep up with the daily incoming volume. And the NAS already runs other services — I didn't want to keep the CPU pegged long-term just to transcribe podcasts.

Thinking it over, I realized there's no real need to force the NAS to do AI inference at all. **What a NAS is genuinely good at is storage, scheduling, and automation.**

So the whole approach became: the NAS handles discovering files, splitting audio, managing tasks, saving results, and serving the web page. The genuinely resource-hungry Whisper transcription gets handed off to the cloud.

Once I reframed it that way, the whole thing suddenly got a lot simpler.

### Two hours of audio a day is basically covered by free tiers

I first checked how much Podsync had downloaded over the past 24 hours — normally around two hours of new audio a day. That's not actually a huge amount.

So I ended up hooking up a service: Cloudflare Workers AI's `whisper-large-v3-turbo`. The web page now separately tracks how many minutes Cloudflare has used that day, and roughly how much quota is left. So opening the page each day tells me roughly how much more audio I can process that day.

### After that, it basically runs itself

The whole thing ended up packaged as a standalone Docker app. Podsync keeps doing what it always did, just downloading podcasts. My program mounts Podsync's audio directory.

Once the container starts, it scans once immediately, then by default scans again every 30 minutes. All of this lives in the web-based settings — which directory to scan, how often, how far back to look, and which takes priority between the primary service and Cloudflare — all configurable from there.

There's also a fairly important detail: how to tell whether a file has already been processed. Since the container might restart, and the Podsync directory always has a lot of historical files sitting in it, it obviously can't re-transcribe everything from scratch every time it starts. So the program generates a fingerprint based on the file path, file size, and modification time. Once a file's been processed, that gets recorded, and the next time it's scanned, it just gets skipped.

There's another detail worth mentioning: when Podsync downloads a larger episode, the file may already show up in the directory while it's actually still downloading. So the program doesn't transcribe a new file immediately — it first confirms the file hasn't changed for at least 60 seconds. Only once it's confirmed stable does it get added to the transcription queue.

By default it only processes episodes added in the past 24 hours. If a task fails, I can just click to re-queue it directly on the web page — no need to SSH into the server and run commands.

### The long-audio problem got solved along the way too

Podcasts have another annoying characteristic: files are often huge. An episode running one, even two hours, is completely normal — but the free Whisper API usually caps individual file size.

So I added an FFmpeg layer in the middle. If it detects audio over 22MB or longer than 20 minutes, it auto-processes it first — converting it uniformly to 16kHz, mono, 48kbps — then splitting it into roughly 20-minute segments. These segments get sent to Whisper separately. Once everything's done, the transcripts get merged back together, with each segment's timestamps restored to its position in the original audio.

So from the user's side, you'd never notice the audio had been split at all — you just see one complete episode with one continuous set of subtitles. The temporary split segments also get automatically deleted once the task finishes, so they don't keep eating up NAS space.

### Once I got this far, I realized subtitles alone weren't quite enough

The text Whisper produces is already quite useful for searching or jumping to a specific point. But if you actually want to read through it from start to finish, the experience is still a bit rough. Speech recognition output often has sentence-break issues, missing punctuation in places, and the occasional homophone typo — especially in longer episodes, reading the raw subtitles is genuinely tiring.

So I hooked in LiteLLM as well. Once Whisper finishes, the subtitles automatically move into another AI processing queue.

What I actually wanted here wasn't for LiteLLM to "summarize" for me — I deliberately put a fairly strict constraint on this. **What I want is cleanup, not summarization.** In other words, whatever the original episode covered should, as much as possible, all stay in there. The AI is only responsible for fixing obvious recognition errors and cleaning up punctuation, sentence breaks, and paragraphing — turning it from a machine transcript into an article a normal person can read comfortably.

So the instructions I gave LiteLLM specifically say: no summarizing, no abbreviating, no deleting opinions, facts, numbers, or examples, and no adding conclusions that weren't in the original. Chinese stays Chinese, English stays English, Portuguese stays Portuguese — no translation either. Only fix recognition errors that can be confidently confirmed from context.

I also added a length check specifically for this. Because when a large model processes long text, sometimes even when you explicitly tell it not to summarize, it'll still take it upon itself to compress things down into a short piece. If the returned article comes back noticeably shorter than the original subtitles, the system treats that run as a failure and refuses to save it outright. That task shows as failed, and I can re-run just the LiteLLM step separately later — without burning through Whisper's quota again.

### In the end, I just built my own podcast reader

Now that I had both subtitles and cleaned-up articles, I ended up building a web page on top of it too. Opening it now shows different shows organized by Podsync's directory structure. You can filter by show, or search directly by filename or show title. Each episode shows its audio length, whether transcription is complete, whether the primary service or Cloudflare handled it, and whether LiteLLM has finished organizing the article.

Clicking into an episode drops you straight into a player. The audio supports Range requests, so it doesn't need to fully download before playing, and the progress bar can be dragged freely. Below that is the raw subtitle track, with every segment carrying a timestamp — click a line, and the player jumps straight to that moment. Conversely, while audio is playing, whatever subtitle line is currently playing gets automatically highlighted. So if I hear something I didn't quite catch, I can just glance at the text below. Or if a particular line in the subtitles looks interesting, one click jumps straight to that point in the original audio.

### Cloudflare's timestamps brought a small problem of their own

The timestamps Cloudflare returns are quite fine-grained — down to the word level in some cases. At first I put all of that directly on the page, and a longer episode could end up generating tens of thousands of page elements. Desktop browsers handled it fine, but on mobile it started getting noticeably sluggish.

So I added an aggregation layer, automatically merging these word-level timestamps into roughly eight-second, one-sentence-ish paragraphs. That cut the number of page elements way down, while keeping the click-to-jump behavior intact.

I also reworked the mobile layout separately. The first version was a full-screen overlay-style reader, which looked fine on desktop, but on mobile, opening the detail view covered the entire screen. Now it's embedded inline in the page instead — the show list, subtitles, and AI article each have their own controlled height and scroll independently. Desktop, tablet, and mobile all work reasonably well now, and both dark and light themes are supported.

### The first real run took nearly three hours

Once the whole system was deployed, the first real run found 4 new audio files, totaling nearly three hours. After that I basically left it alone. The system checked the files itself, compressed and split them itself, and first tried transcribing with the primary Whisper service. When the primary service hit a quota limit, it automatically switched to Cloudflare. Once all the subtitles were done, it automatically handed things off to LiteLLM to organize into articles. By the time I checked the web page again, all 4 episodes had been fully processed — no failed tasks, no backlog.

The full pipeline now essentially looks like: **Podsync downloads a podcast → the NAS auto-discovers it → checks whether the download is complete → deduplicates by fingerprint → auto-compresses and splits long audio → Whisper transcribes → automatically falls back to Cloudflare if the primary service is unavailable → merges subtitles and timestamps → LiteLLM corrects recognition errors and organizes the article → play, jump to a point, and read, all from the web page.**

### What I ended up with is a bit different from what I originally imagined

At the start, this was really just meant to solve a simple problem: **how to automatically turn podcasts downloaded to the NAS into text.** But by the time it was done, it had turned into more than just a transcription tool.

Now, once Podsync downloads an episode, I basically don't need to touch anything else. Come back to the web page a bit later, and the audio, subtitles, and cleaned-up article are all sitting there already. Want to listen — go ahead. Want to skim quickly — check the subtitles. See something interesting — click the timestamp and jump straight to it. Just want to read quietly — go straight to the LiteLLM-organized article. And every episode ultimately stays on my own NAS, searchable and kept long-term.

I've increasingly come to feel that, for an older NAS like the DS218+, there's no need to insist on running every AI workload locally. Making it run large models just isn't what it's good at. But treating the NAS as an **automation hub** — that, I think, actually fits it really well. The files are there, Podsync is there, Docker is there, and task scheduling, FFmpeg, the database, and the web page are all there too. When real compute is actually needed, just call out to the primary Whisper service, Cloudflare, and LiteLLM out in the world. That sidesteps the old hardware's performance limits, while basically covering each day's new podcast volume using free quota alone.
