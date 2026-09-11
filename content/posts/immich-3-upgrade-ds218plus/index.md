---
title: "Immich 3.0 Upgrade Log: Taking a DS218+ From 2.7 to 3.0"
date: 2026-07-09T18:00:12+00:00
summary: "The official 3.0 release with built-in AI photo editing was tempting enough to risk an upgrade on a stable NAS setup — the process went smoothly, with the only surprise being a slow first-boot database migration that looked like a crash."
tags: ["Self-Hosting"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/07/09/Pasted-image-20260709160338.jpg"
---

### Why upgrade?

I recently saw that Immich had officially released version 3.0. As one of the most important apps on my NAS, I naturally paid attention to this major version update right away.

Before upgrading, I first compared what new features in 3.0 might actually be worth it, relative to the 2.7 version I was currently running.

This 3.0 release isn't a minor version bump — it's a fairly substantial feature upgrade.

A few new features that caught my eye: **built-in AI photo editing**, letting you do simple photo edits directly inside Immich without exporting to other software; a more polished photo-management experience, including better browsing and refined details; continually strengthened search and AI capability, with more room for vision-model-based expansion down the line; and an upgraded underlying architecture, with the database's vector engine and other components optimized to support future features.

For me, **the photo-editing feature is the most valuable part**. A lot of my photos get auto-synced from my phone to the NAS, and occasionally I need to crop, adjust brightness, or make some simple edit — previously that meant downloading it to my phone or computer to process, then re-uploading. If I can now do that directly inside Immich, the whole photo-management workflow becomes a lot more convenient.

After weighing it all, I decided to upgrade the family's Synology DS218+ to Immich 3.0.

### My environment

Before upgrading: Synology DS218+, DSM 7, deployed via Docker Compose, Immich 2.7.x, the official PostgreSQL database image, over one hundred thousand photos, with AI facial recognition already completed.

Since the photo library was already fairly large, I decided to follow the official recommendation and do a full backup first before upgrading.

### Pre-upgrade checks

First, I confirmed my current Docker Compose configuration. Checking it, I found I was already on the official new-style Compose file, and the database had already been switched to the new VectorChord image — so the database-migration steps in the official upgrade docs didn't apply to my setup, and I didn't need to touch the database config at all.

I then backed up a few key files: the PostgreSQL database, `docker-compose.yml`, and `.env`. That way, even if something went wrong during the upgrade, I could recover quickly.

### Starting the upgrade

The upgrade process itself turned out to be very simple — basically just following the official docs. First, pull the latest images:

```bash
docker compose pull
```

Then restart the containers:

```bash
docker compose up -d
```

The only issue I ran into during the whole process was that the image download was fairly slow. Immich's images are hosted on GitHub Container Registry (GHCR), and one layer of the `immich-server` image, over 300MB, downloaded very slowly — on my network it took quite a while to finish. Worth a bit of patience here, or configuring a proxy for the NAS ahead of time.

### The first boot after upgrading

Once the image update finished, I immediately tried to open `http://NAS_IP:2283` — and the browser just wouldn't load it. My first thought was that the upgrade had failed. So I started troubleshooting step by step.

First, I checked the container status with `docker compose ps`, and found: PostgreSQL fine, Redis fine, Machine Learning fine, and the Immich Server had started but was still showing `health: starting`.

I kept looking at the logs with `docker logs immich_server`, which showed "Running migrations," and I could see the system was working through the facial-recognition and vector indexes for over a hundred thousand photos.

That's when I realized: it wasn't a failed upgrade — **Immich 3.0's very first startup was running a database migration and index check.** For a DS218+ with only two CPU cores, this step just takes a while. As long as I was patient, once the service finished initializing, the health status went from "starting" to "healthy," and the web page came back up normally.

### Wrap-up

The whole upgrade went more smoothly than I expected — no compatibility issues, and no need to re-import photos or re-run face recognition. Immich has been gradually evolving from a great open-source photo app into an increasingly full-featured personal photo-management platform. For me, the biggest thing I'm hoping to get out of this upgrade is being able to do everyday photo edits directly on the NAS, further cutting down on shuffling photos back and forth between different devices.
