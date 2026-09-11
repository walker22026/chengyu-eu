---
title: "100 Projects Before I Retire, #4: A MinIO Object Storage Service"
date: 2025-06-16T09:00:05+00:00
summary: "Turning a cheap, low-spec server into a dedicated S3-compatible object store for images, audio, and video, instead of hosting another website on it."
tags: ["Self-Hosting", "MinIO"]
categories: ["Blog"]
---

As part of my "100 projects before I retire" plan, I recently finished project #4: setting up a network storage service. This time I picked up a very cheap server — modest specs, just a 1TB disk and 1GB of RAM — but its one advantage was plenty of storage space. So instead of hosting a website on it, I decided to turn it into an **object storage service**.

### Why bother with this?

As my projects have piled up, so have the images, audio, and video files that go with them. Keeping all of that on a website server gets harder to manage over time, and access gets less reliable. Running a dedicated object storage service instead brings a few real benefits:

- **Centralized resource management** — images, audio, and video all live in one place, which makes them easier to reference and maintain.
- **More reliable access** — paired with a CDN, file loading speeds up considerably.
- **External links and access control** — you can generate temporary access links per file, which is both convenient and safer.
- **Good compatibility** — this system speaks the same API as Amazon S3, so migrating or upgrading later should be seamless.

### What I used

I went with an open-source project called **MinIO** — a very lightweight object storage system with a clean interface and practical features, and it doesn't ask much of the server it runs on, which made it a good fit for this particular old, underpowered machine.

Paired with a domain and encrypted access through Cloudflare, I ended up with an object storage platform that's both secure and easy to use. Now, whether I'm uploading files, browsing what's stored, or pulling images into a website, it's all pretty painless.

### How it's working out

Several of my projects are now hooked up to this storage system: images in blog posts now load through the storage service and load faster, assets for video-narration projects are stored in one place instead of scattered around, and family videos and travel photos now have a private cloud backup space too.
