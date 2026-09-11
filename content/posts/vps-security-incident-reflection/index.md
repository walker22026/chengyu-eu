---
title: "Reflections After a VPS Security Incident: From Discovery to Building a Full Monitoring System"
date: 2026-07-02T12:37:21+00:00
summary: "A cryptomining-style trojan ran undetected on my Oracle Cloud VPS for nearly 20 days — the real lesson wasn't fixing that one box, but realizing monitoring itself needs to be monitored."
tags: ["Self-Hosting", "Troubleshooting"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/07/02/Pasted-image-20260702103300.jpg"
---

Recently spent nearly all my free time on VPS security investigation.

It started when an Oracle Cloud VPS was found running what looked like a malicious program. In the end it didn't cause any obvious damage, but the whole incident drove home a point:

**The real danger isn't that a server gets attacked — it's that it gets attacked for a long time while you have no idea.**

This incident pushed me to redesign my entire VPS security-monitoring system from scratch. From the initial discovery of the anomaly, through emergency response, to building out the monitoring system and continuously upgrading it, it really shifted how I think about operations.

### I. Discovering the problem: a security risk that had persisted for days

What I first received wasn't an intrusion alert — it was a notice from a self-written inspection script flagging a suspicious high-CPU process.

Logging into the server, I quickly found something off. The process was running out of `/tmp`, with a randomized filename, chewing up a lot of CPU over a long period. Its parent process pointed to LiteLLM, and further digging turned up several Python loaders and already-exited zombie processes.

By the time I looked further, every program involved had already been deleted, leaving only traces of having run.

The whole behavior pattern matched a very common style of Linux cryptomining trojan: download the malicious program, run it temporarily, delete itself, leave no files behind.

I never managed to find a mining-pool address or a sample of the program itself, so I couldn't confirm it with 100% certainty — but taken together, the behavioral characteristics point pretty clearly to a typical CPU-mining trojan.

What really unsettled me: based on the logs, this program had been running for close to twenty days. If the inspection script hadn't happened to trigger, I wouldn't have even known the server had ever executed a malicious program.

### II. Emergency response: contain the risk first, find the cause second

Once I confirmed the anomaly, I didn't rush to find the entry point — I focused on containing the risk first.

First I checked whether the system had been further compromised: SSH login records, `authorized_keys`, cron jobs, systemd services, network connections, newly listening ports, Docker containers, temp directories. Fortunately, I found no persistent backdoor and no unusual network connections.

I then checked every API key stored in LiteLLM. Since the server had run an unknown program, and even though I found no signs of key abuse, I rotated all of them anyway to avoid the risk spreading further — including the Gemini API key and the LiteLLM master key.

At the same time, I reassessed the risk around Oracle Cloud's Free Tier. Based on their official abuse policy, as long as there's no sustained mining, DDoS, or spam activity, the odds of the account getting banned are fairly low.

In the end, I decided: **this server would no longer be used — I'd reinstall it and rebuild a trusted environment.** When it comes to security, there's ultimately no meaningful difference between "suspected compromised" and "actually compromised."

### III. Rethinking monitoring: without monitoring, there is no security

One thing kept coming up throughout the investigation: a lot of the anomalies had already happened — but left no evidence behind. For example: the `/tmp` program had already been deleted, the Python loader had already been deleted, the temporary port had already closed, high CPU usage had already returned to normal. By the time someone logged into the server, the scene had already vanished.

That made me realize: **the traditional approach of "notice a problem, then log in and investigate" is increasingly unable to keep up with today's malware.**

So I started redesigning the entire monitoring system from the ground up.

### IV. Building a multi-layer monitoring system

I've now put together an initial four-layer monitoring architecture.

**Layer one: Healthchecks.** Monitors whether every scheduled task is still running normally. If any cron job stops running, I get notified immediately.

**Layer two: Sentinel self-checks.** Every server periodically sends a self-check message to Telegram, e.g. "✅ Sentinel self-check: Telegram channel normal." If a given server suddenly stops sending self-checks — even if Healthchecks itself looks fine — it means the monitoring program itself may have failed. During this investigation, I actually found three servers that had stopped sending Sentinel self-checks. The VPS itself was fine, Healthchecks was fine, but it turned out there were gaps in how Sentinel had been deployed. This confirmed: **the monitoring program itself also needs to be monitored.**

**Layer three: Security scanning.** Currently able to automatically detect: new listening ports, suspicious high-CPU processes, SSH login changes, root logins, newly added users, systemd service changes, cron changes, Docker anomalies, API key risk, and executable files in temp directories. All anomalies get pushed to Telegram in one place.

**Layer four: Manual review.** Automated monitoring is responsible for catching problems first. The actual judgment call still needs a human, because security's biggest enemy isn't necessarily a missed detection — it's also false positives.

### V. The monitoring system's first "exam"

Right after the monitoring went live, I quickly got a string of alerts. One flagged a new UDP listening port — I logged in right away to check, and it turned out to just be system components like WireGuard and rpcbind. Another flagged suspicious high CPU — and it turned out to be the inspection script itself, misidentifying its own `ps` command process as high-CPU usage.

None of these false positives were a real security risk, but they made something clear to me: **monitoring not only needs to catch anomalies, it needs to minimize false positives as much as possible.** Otherwise, over time, the genuinely important alerts start getting ignored too.

### VI. Continuing to upgrade the monitoring system

Based on these investigations, I've now planned out the next stage of upgrades for Sentinel, including: secondary confirmation for new ports to filter out transient listeners, a process whitelist mechanism, automatic evidence preservation, SHA256 verification, hash monitoring for executable files, detection of Docker config changes, monitoring for Caddy/Xray config changes, API key leak detection, automatic tracking of how many servers are online, and automatic consistency checks between Healthchecks and the server inventory.

Going forward, when I get an alert, I don't just want to know "an anomaly was found" — I want to be able to see directly: which process, who started it, where it came from, whether it's on the whitelist, its risk level, and whether evidence has been preserved. The real goal is being able to judge the risk the moment an alert arrives, instead of having to log into the server and start investigating from scratch.

### VII. The biggest takeaway

The biggest thing I got out of this wasn't finding some specific vulnerability or confirming the exact attack vector — it was rethinking what security operations actually means.

I used to think security meant a system that never gets attacked. Now I believe more in this: security means being able to detect an attack quickly and recover fast.

For someone maintaining several VPS instances on their own, absolute security just isn't realistic. What's actually achievable is building a continuously running monitoring system: one that can catch anomalies, verify that the monitoring itself is working, preserve evidence as much as possible, keep reducing false positives, and respond in the shortest time possible.

Security isn't a few installed tools, and it isn't a one-time hardening pass — it's an ongoing engineering effort. This suspected-trojan incident felt like an expensive but worthwhile hands-on lesson. It pushed me from a mindset of "investigate after something breaks" toward "continuously monitor, detect quickly, respond promptly."

Going forward, I'll keep refining Sentinel, turning it into a lightweight security-monitoring platform suited for managing multiple personal VPS instances — so my servers become genuinely observable, traceable, alertable, and recoverable.
