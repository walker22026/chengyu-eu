---
title: "A Failed Attempt: Tracking a Google Account Suspension and Appeal"
date: 2026-01-29T15:00:09+00:00
summary: "Registered a new Google account to pay for steadier API access after hitting rate limits on the free tier — and got flagged as bot-created within a day of paying."
tags: ["Google", "AI"]
categories: ["Blog"]
---

### Background

I'd been running Clawdbot against Google Gemini's free tier for a while, but kept hitting Google's rate limits or safety-review triggers, which meant the account kept getting temporarily frozen — not great for the stability of an automated pipeline. To get more consistent API responses and a higher quota, I decided to drop the free tier and register for one of Google's paid plans instead.

### Registering and paying

Since Google Cloud and its paid services aren't available to mainland Chinese accounts, I registered a US Google account to make sure the service worked properly and stayed accessible. The payment came to $30, and on the bank's side it showed as an "overseas online sale — authorized" (authorized/pending), meaning the funds were held but not yet finally settled.

### Getting flagged and suspended

Shortly after the payment went through (on January 29, 2026), the system flagged the account as "created by a bot or script" or "linked to multiple other accounts," and suspended it immediately. My guess is that registering the account, attaching an overseas card, and making a fairly large payment all within a short window tripped some baseline fraud-detection logic in Google's global security system.

### Where things stand

I formally submitted a reinstatement appeal on January 29, asking Google to confirm the account was registered manually by a real person for legitimate development purposes, to restore normal access, and — if the account can't be restored — to reverse the $30 pending charge. I'm currently waiting on a reply from Google's review team, which typically takes 24–72 hours.

In the meantime, I've picked up a paid model plan through OpenRouter instead, starting with their simplest paid tier.
