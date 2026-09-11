---
title: "OpenClaw's Weekend \"Zero-Cost\" Overhaul"
date: 2026-04-11T17:33:00+00:00
summary: "When my free Google Cloud credits ran out and Gemini started rate-limiting, I rebuilt OpenClaw's model chain into a three-tier free-and-cheap failover setup — and rethought which use cases were still worth the tokens."
tags: ["Self-Hosting", "OpenClaw"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/04/11/Pasted-image-20260411101952.jpg"
---

### Background

This weekend I spent a good chunk of time doing a systematic overhaul of OpenClaw. The trigger was simple: my previously ordered $300 free Google Cloud credit expired, and Gemini API calls started returning widespread `429`s (rate limit exceeded) — the bot stopped responding entirely.

I needed to find a way to keep the system running at the lowest possible cost, ideally zero. I ended up settling on a **three-tier model chain** that, all told, runs at essentially zero additional cost.

### The fix: a three-tier model chain

- **Primary model: ChatGPT Plus (`openai-codex/gpt-5.4`)**

  Subscribed through a low-price region, so the cost itself isn't high. With my language exam coming up soon, I'm not doing much Codex coding right now, so Premium usage is limited — this serves as my daily main model.

- **Middle-tier fallback: 3 free NVIDIA NIM models**

  Brought in Kimi K2.5, MiniMax M2.5, and GLM-5. These switch in automatically whenever the primary model's quota runs out or hits an issue — completely free.

- **Bottom-tier safety net: OpenRouter Free**

  `openrouter/free` automatically picks the best available free model, with 200 requests a day — the final fallback, at no cost at all.

### Key changes, logged

#### ① Wiring up the NVIDIA NIM API

1. Registered an account at `build.nvidia.com` and generated an `nvapi-xxx` API key.
2. Added a new `nvidia:default` profile in `auth-profiles.json` with the API key.
3. Added a new `nvidia` provider in `models.json`, pointing its base URL at `https://integrate.api.nvidia.com/v1`.
4. Hung Kimi K2.5, MiniMax M2.5, and GLM-5 under the `nvidia` provider, sharing the same API key.
5. Updated `clawdbot.json` to add all three models to `models.providers`.

#### ② Pitfalls hit along the way

- **`gateway` and `pm2` process conflict:** having both `systemd` and `pm2` manage the process caused port 18789 to keep getting grabbed repeatedly. Stopping pm2's `oc-gateway` fixed it — `systemd` should be the sole manager.
- **Model ID routing errors:** splitting the third-party models (moonshotai / minimaxai / z-ai) out into their own separate providers caused 404s. Ended up hanging them all under the `nvidia` provider instead; OpenClaw's correct routing format is `nvidia/moonshotai/kimi-k2.5`.
- **`clawdbot.json` doesn't support the `alias` field:** only `agents/main/agent/models.json` supports aliases — writing one into `clawdbot.json` makes the config invalid and the gateway refuses to start.
- **`openai-codex` token expired:** re-ran the OAuth flow via `openclaw configure`; the new token was written to the `openai-codex:walker.wangw@gmail.com` profile, and I had to manually update the `lastGood` field to point at the new profile.
- **Google Gemini kept 429ing:** demoted it to fourth in the fallback order, only triggered once the first three tiers have all failed.

#### ③ The final fallback chain

1. **Primary:** `openai-codex/gpt-5.4`
2. **Fallback 1:** `nvidia/moonshotai/kimi-k2.5`
3. **Fallback 2:** `nvidia/minimaxai/minimax-m2.5`
4. **Fallback 3:** `nvidia/z-ai/glm5`
5. **Fallback 4:** `google/gemini-3-flash-preview`
6. **Fallback 5:** `openrouter/free`

### After all this time, how my usage has shifted

OpenClaw's buzz domestically is cooling off fast, but I've kept using it — just with the use cases shifting. Some features stuck around, others I deliberately dropped.

#### ⏰ Scheduled reminders (kept)

Still using this. This time around I also set up a cron job to push a Telegram reminder 48 hours before the Codex token expires.

#### 📰 News summarization (kept)

My most-used feature right now. ChatGPT and Gemini often say they "can't read the full article" on long pieces — just handing the link to OpenClaw gets a full read, no manual copy-pasting needed.

#### 📈 Investment platform automation (kept, logic reworked)

Financial info and account updates get pushed via Telegram. The role of the decision logic has fundamentally changed:

- **Before:** the model drove decisions directly.
- **Now:** the model only handles intent recognition and wording — **all decision strategy is implemented entirely in backend code.**
- *Why:* this avoids the model making misjudgments from randomness on sensitive, important operations, keeping things deterministic.

#### 🃏 Language learning / flashcards (removed)

Dropped this one deliberately, for two reasons:

1. **Cost:** using an LLM to memorize vocabulary burns an enormous amount of tokens — nowhere near as cost-effective as spending that same quota on coding. Switched to RemNote for flashcards instead, which syncs across computer, phone, and tablet, is fast, and honestly feels better.
2. **Accuracy:** I'd previously had OpenClaw bulk-supplement knowledge points to help with studying, but because the model used wasn't consistent call to call, its take on the same knowledge point would contradict itself over time, creating logical conflicts. An LLM's randomness is a real liability for memorization tasks — you never know if today's answer matches what it said last week.

### Wrap-up

This overhaul gave me a clearer sense of where an LLM's usefulness actually ends: an LLM is good as an accelerator, not as an authoritative source. Using it to interpret, polish, and push notifications is a good fit; letting it directly drive decisions or build a knowledge base is where the risk gets amplified. Zero-cost survival achieved — still running.
