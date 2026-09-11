---
title: "From Google Photos and iCloud to Immich: Building My Own Photo Management Hub"
date: 2026-06-15T08:00:36+00:00
summary: "Photos scattered across Google Photos, iCloud, a Huawei phone, a DJI drone, and action cameras finally got consolidated into one self-hosted, deduplicated Immich library — though my aging Synology DS218+ is starting to show its limits."
tags: ["Self-Hosting"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/06/14/Pasted-image-20260615073932.jpg"
---

### Foreword

Photos capture the small moments of everyday life. For the past decade and more, I've relied on Google Photos as my main platform for storing and managing them.

When Google Photos first launched, it was arguably one of the best photo management tools around — unlimited storage, powerful search, excellent facial recognition, and solid timeline organization meant I uploaded nearly every photo I had to it.

But once Google dropped the free unlimited-storage policy, photo storage gradually turned into an ongoing expense. As the number of photos and videos kept growing, I found myself paying for extra space every month. On top of that, network conditions made accessing Google Photos increasingly inconvenient.

The bigger problem, though, was that as I accumulated more devices, my photos started scattering across multiple platforms.

- Google Photos: my main photo library
- iCloud Photos: some of my iPhone photos
- Local photos on my Huawei phone
- Photos and videos from a DJI drone
- Action-camera footage
- Local storage on the NAS

Because different devices and platforms sync in different ways, the same photo would often end up in multiple places at once.

The result: more and more photos, and yet actually finding a specific one got harder and harder.

So I decided to build a unified photo-management hub using Immich, to bring everything together and handle deduplication automatically.

### Why Immich

When picking a photo-management platform, I compared several options: Synology Photos, PhotoPrism, Nextcloud Photos, and Immich.

I ultimately went with Immich, for three main reasons.

**First, the experience is closest to Google Photos.** Immich offers timeline browsing, map browsing, facial recognition, smart search, automatic phone backup, and multi-user management — for a NAS user, that's already enough to replace Google Photos.

**Second, full control over the data.** Every photo lives on my own NAS. No more dependence on a third-party platform, and no more worrying about future policy changes bringing extra costs.

**Third, strong deduplication.** This was actually the thing I cared about most for this migration. My photo sources were genuinely messy — Google Photos, iCloud, a Huawei phone, DJI gear, and various local directories — with a lot of overlap between them. Immich itself has solid duplicate-photo detection, and the immich-go tool further strengthens deduplication during import. That means no matter where a photo originally came from, only one copy ends up in Immich in the end — exactly the outcome I wanted.

### Step 1: upgrading Immich

Before starting the migration, I upgraded Immich itself. My installed version was fairly old, v1.135.3; the target was v2.7.5.

Since this crossed several major versions, I did a full backup first — the PostgreSQL database, the Immich library, the docker-compose config, and the `.env` config — before running the upgrade. Afterward, I re-indexed the database and media files to get ready for the large-scale import to come.

### Step 2: importing Google Photos

Google Photos was my core photo library. Years of accumulation meant the exported Google Takeout data came out to hundreds of gigabytes.

**Google Takeout export.** Exporting through Google Takeout eventually produced dozens of archive files.

**The batch-download problem.** With that many files, downloading them one by one manually would have been extremely tedious, so I wrote an automated download script using browser-cookie authentication. It handled reading the cookie automatically, downloading each archive, auto-retrying on failure, and resuming interrupted downloads — letting the whole thing run unattended.

**Importing with immich-go.** Google Photos' biggest headache is metadata — a lot of the capture time and album info doesn't live in the photo file itself, but in a matching JSON file. So I couldn't just unzip everything and upload it directly. I ended up using `immich-go upload from-google-photos` for the import, which automatically restored capture times, restored albums, linked up metadata, and detected duplicates along the way. The whole process took a while, but it successfully preserved the complete history from Google Photos.

### Step 3: importing iCloud Photos

Compared to Google Photos, exporting from iCloud turned out to be even more of a hassle.

**Apple's data export.** Apple offers a data-export feature; requesting it generates a download link. The catch: that link stays valid for a very short time — in my testing, it expired after roughly ten-odd minutes.

**Brute-force concurrent downloading.** Facing that constraint, I went with the most direct approach: grab every download link at once and kick off all the downloads simultaneously, to make the most of the short validity window. Crude, but it worked — I successfully pulled down every photo and video.

**Importing with immich-go.** The import method was similar to a regular directory import: automatically reading EXIF data, automatically identifying capture times, and automatically deduplicating. A large share of photos that already existed in Google Photos got correctly flagged as duplicate assets, so the final photo count didn't end up doubling.

### Step 4: importing DJI, action-camera, and drone footage

Beyond phone photos, I also had a large amount of video from other devices — a DJI drone, DJI Action, GoPro, other action cameras, and more. Most of these files were already organized into directories on the NAS. Since this data doesn't carry complex metadata, handling it was relatively simple — just imported straight through immich-go, which automatically recursively scans every directory while preserving video capture times, file metadata, and directory structure.

### immich-go: the real workhorse of the whole migration

Across the entire migration, immich-go was the tool that genuinely made it all work. Even though the data sources were completely different from each other, all of them could ultimately be imported by just adjusting the command's parameters — everything funneling into a single unified Immich library. That dramatically cut down the complexity of the whole migration.

### The end result

After several days of organizing and importing, I finished consolidating my photo library. Every source now feeds into Immich, and thanks to its deduplication: duplicate photos get automatically identified, duplicate videos get automatically filtered out, and only a single unique copy of each is kept. The end result is one unified, clean, and sustainably maintainable photo library.

### Immich's one drawback: my DS218+ is starting to struggle

Even though the migration itself went smoothly in the end, actually using the system afterward surfaced a new problem: Immich keeps getting more capable, and my DS218+ is starting to fall behind.

My NAS is a Synology DS218+, a pretty classic model, spec'd with an Intel Celeron J3355, 2 cores, and up to 6GB of RAM. Back when it was just running Synology Photos, that spec was basically no problem at all.

But Immich has developed rapidly in recent years, especially with the addition of facial recognition, smart search, CLIP vector search, video thumbnail generation, map indexing, and machine-learning models — all of which demand real CPU and memory resources behind the scenes.

While importing tens of thousands of photos and hundreds of gigabytes of video, I could clearly feel the DS218+ approaching its limits. It was common to see `immich-machine-learning`, `ffmpeg`, and thumbnail-generation tasks sitting at high CPU usage for extended periods. Sometimes, even pausing the task queue, the background would still take a long while to finish jobs already submitted for indexing.

Especially during face re-recognition, video transcoding, large-scale imports, and machine-learning model updates, the DS218+'s responsiveness noticeably dropped — sometimes even affecting other apps running on the NAS. For simply browsing photos this isn't a big deal, but if I want to make full use of everything Immich's latest version offers, the hardware has already started to become the bottleneck.

### What's next

Immich is now my sole photo-management platform, so the focus going forward shifts from migration to infrastructure upgrades. I'm planning to eventually move to more capable hardware — something like a DS923+, a DS1522+, a self-built NAS, or a mini PC running Docker with Immich — to let Immich's AI capabilities run unconstrained.

For now, I think the DS218+ can still handle photo storage, everyday browsing, and automatic phone backup just fine. But once the photo count climbs past the tens of thousands with every AI feature enabled, upgrading the hardware becomes just a matter of time.

### Afterword

Looking back, the biggest payoff from this migration wasn't the money saved by dropping a Google Photos subscription, and it wasn't just consolidating photos from multiple platforms into one system either.

What matters more is that I finally have a photo-management setup that's entirely my own. From the free era of Google Photos, through paying for extra storage, to finally completing the move to Immich, photo storage for me has gone through a real shift — from depending on a cloud service to actually owning my data.

Today, photos and videos from Google Photos, iCloud, a Huawei phone, a DJI drone, and various action cameras have all converged into Immich, kept unique through automatic deduplication. Going forward, even if I switch devices, switch cloud services, or even switch NAS hardware, the photo library itself won't be affected.

And for someone who's accumulated more than a decade of photos and videos, that kind of certainty and control probably matters more than any single feature.
