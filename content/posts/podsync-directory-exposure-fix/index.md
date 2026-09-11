---
title: "Fixing an Exposed Directory Listing in My Podsync Docker Service"
date: 2025-07-09T11:00:06+00:00
summary: "A missing index.html meant Podsync's web server was serving up a full directory listing of everything it had synced — here's the one-file fix."
tags: ["Self-Hosting", "Security", "Docker"]
categories: ["Blog"]
---

I recently deployed Podsync on my NAS — a tool that turns YouTube channels or RSS feeds into podcast subscription URLs, so I can subscribe and listen offline through a podcast client. After setting it up, though, I noticed a problem: its web server was exposing the entire contents of its output directory.

Visiting Podsync's address in a browser showed something like:

```
Index of /
- index.xml
- feed.json
- video001.mp4
```

That meant anyone who knew the URL could see — and even download — every file it had synced. For anything reachable on the open internet, that's a real security problem.

### A simple, effective fix: add a default `index.html`

The fix turned out to be simple: drop an `index.html` file into Podsync's output directory. The web server will load that file first, which replaces the default directory listing.

Here's an example file:

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Private Podsync Service</title>
</head>
<body>
  <h1>Welcome to Podsync</h1>
  <p>This page is for a private subscription only — directory browsing is disabled.</p>
</body>
</html>
```

Just drop that HTML file into Podsync's output directory (something like `/volume1/docker/podsync/`), refresh the browser, and instead of an "Index of /" listing, you'll land on your own custom page.

### The result

With that in place, anyone visiting the Podsync address now only sees the page I set up — no file listing at all. For anyone running Podsync on a home network or exposing it to the public internet, this is a genuinely useful bit of hardening.
