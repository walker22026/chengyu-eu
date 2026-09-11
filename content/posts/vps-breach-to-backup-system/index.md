---
title: "From a Compromised VPS to Building a Real Website Backup System"
date: 2026-07-03T10:00:10+00:00
summary: "After cleaning up malware off a VPS, the bigger realization was that defense alone isn't enough without recoverability — so I built a four-layer backup system covering local snapshots, scheduled automation, off-site NAS sync, and recovery documentation."
tags: ["Self-Hosting", "Troubleshooting"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/07/02/Pasted-image-20260703094301.jpg"
---

The recent incident where malware got deployed on my private VPS actually affected me less through the process of removing the virus, and more by making me realize another problem that's easy to overlook — **data safety**.

Before, I mostly focused on server security: closing ports, minimal privilege, firewalls, monitoring alerts... all of that matters. But once I really sat down and thought it through, I realized that no matter how thorough your security measures are, nobody can guarantee a server will never run into trouble.

What actually reduces risk isn't just defense — it's **recoverability**.

So after finishing the Sentinel security-monitoring system, I started filling in another gap: an **application data backup system**.

### Why bother with backups

For my website, the genuinely valuable part isn't the code. Code can be restored from GitHub and redeployed; what's genuinely hard to recover is the business data sitting in the SQLite database, the config files, and everything that accumulates as the site keeps running.

If a server gets deleted, a disk fails, or it gets attacked again, no backup means that data could be gone permanently. So this round of work had one clear goal: **whichever server runs into trouble, the business should be recoverable in the shortest time possible.**

### Layer one: automatic local backups on the server

First, I added a standalone backup script for the project. The backup strategy splits into two categories.

**Data backups:** run automatically every day, using the SQLite Backup API to export the database (avoiding a direct copy of a live SQLite file), automatically verifying database integrity (`PRAGMA integrity_check = ok`), and keeping 60 days of history by default.

**Code backups:** packaged automatically every week, automatically excluding irrelevant content like `.git`, `.venv`, cache directories, runtime data, and the backup directory itself, keeping 12 weeks of history by default.

This way, every backup produces a timestamped archive, and I can roll back to any historical version at any time.

### Layer two: automated scheduling via systemd

Backups can't depend on being run manually. So I added systemd timer jobs to fully automate the whole process. Currently: data backups run daily, code backups run weekly. All the timers are installed and verified to trigger correctly. Going forward, even if no one logs into the server for a long stretch, new backups will keep getting generated.

### Layer three: off-site backup to the NAS

Local backups don't solve the problem of the server itself being destroyed. If the VPS gets deleted, the disk fails, or the whole machine becomes unrecoverable, every local backup gets lost right along with it.

So I added an **off-site backup** as well. The whole setup uses a NAS-initiated pull model:

```
VPS
    │
SSH + rsync
    │
Synology NAS (Docker)
```

The NAS connects to the VPS on a schedule through a Docker container and syncs the server's backup directory down locally. Once synced, the backups live in their own dedicated data partition on the NAS, not taking up system-partition space, and leaving room to easily extend the setup to more projects later. This way, even if the VPS is completely destroyed, a full copy of both the data and the code backups remains intact.

### Layer four: recovery documentation

Backups only really prove their worth at the moment of recovery. So I also put together a complete backup document covering: the local backup method, how the automated backup is deployed, the NAS Docker configuration, the data-recovery process, and the code-recovery process. I also updated the project status doc and GitHub configuration alongside it, so the whole backup system stays maintainable long-term instead of depending on memory.
