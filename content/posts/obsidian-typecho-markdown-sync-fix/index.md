---
title: "Keeping Markdown Formatting Intact When Obsidian Syncs to Typecho"
date: 2024-08-04T00:00:38+00:00
summary: "My Obsidian-based writing setup was publishing beautifully, except synced posts landed in Typecho full of raw HTML clutter — a hidden XML-RPC setting fixed it."
tags: ["Obsidian", "Typecho", "Self-Hosting"]
categories: ["Blog"]
featureImage: "https://images.chengyu.eu/file/613cba5ad321f8124d382.jpg"
---

I'm genuinely happy with the little Obsidian-based writing setup I've built. Obsidian's frictionless writing experience means I can jot things down anywhere — at work, at home, on the move — and then, whenever I have time, tidy the notes up and publish them by category through a background file-sync process.

### Multi-platform sync

1. **`Blog` folder** — drop an article in here, and a Python script running on my home NAS automatically syncs it to my blog.
2. **`DayOne` folder** — drop it here, and it publishes automatically into the DayOne app, for private storage.
3. **`Media` folder** — drop it here, and the Markdown file gets converted into a video, ready to publish across various platforms.

### The one imperfection

The thing that kept bugging me: once a Markdown file synced over to Typecho, the Markdown-aware editor would fill up with a wall of raw HTML — making it basically impossible to go back and edit the post on the site itself.

![Raw HTML clutter after sync](https://images.chengyu.eu/file/613cba5ad321f8124d382.jpg)

### The fix

Today, while setting up a visitor-tracking plugin on the site, I stumbled onto a setting that controls whether content submitted via the XML-RPC interface keeps its Markdown formatting instead of being converted to HTML. Flipping that setting solved the problem completely — the content is much tidier now.

![The XML-RPC setting](https://images.chengyu.eu/file/e43e6d6a3df675a5d0cc4.jpg)

### Takeaway

Whether it's private notes or public posts, Obsidian and this sync setup let me manage and publish content far more efficiently. With the "can't cleanly re-edit synced posts" problem solved, I can finally let that particular obsession go.
