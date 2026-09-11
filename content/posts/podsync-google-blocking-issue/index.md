---
title: "Podsync Stopped Fetching New Episodes — Turns Out It Was Google"
date: 2024-08-10T10:00:13+00:00
summary: "A morning-commute podcast habit broken by an upstream block, not anything on my end."
tags: ["Self-Hosting", "Troubleshooting"]
categories: ["Blog"]
featureImage: "https://images.chengyu.eu/file/ac5e3d87c167e04d1a3aa.jpg"
---

### The problem

Podsync is a Docker service I genuinely like — listening to it during the morning commute has become a habit. Except it suddenly stopped being able to fetch the latest content.

### Digging in

After restarting it and searching around on GitHub, it turns out this isn't an isolated issue — it's Google blocking access on their end.

![The error](https://images.chengyu.eu/file/ac5e3d87c167e04d1a3aa.jpg)

### For now

Disabled Podsync for the time being, while I look at alternatives. Keeping an eye on Podsync's updates in the meantime, in case the team resolves it and service comes back.
