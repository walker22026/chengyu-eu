---
title: "Podsync Download Troubleshooting Diary"
date: 2026-04-29T09:00:07+00:00
summary: "Podsync started throwing YouTube bot-verification and rate-limit errors — the fix was exporting browser cookies into the container's mounted data directory and wiring them into yt-dlp's args."
tags: ["Self-Hosting", "Troubleshooting"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/04/28/Pasted-image-20260429085403.jpg"
---

### The symptom

Podsync's logs were repeatedly showing two kinds of errors.

Bot verification error:

```
ERROR: Sign in to confirm you're not a bot. Use --cookies-from-browser or --cookies
```

Rate limit error:

```
WARN: server responded with a 'Too Many Requests' error
```

### Root cause analysis

| Error type | Cause |
| --- | --- |
| Bot verification | yt-dlp wasn't carrying any login credentials, so YouTube flagged it as a bot |
| Too Many Requests | The NAS's outbound IP was making requests too frequently and got rate-limited, made worse by multiple containers downloading at once |

### The fix: configuring cookies

**Step 1: export cookies**

Install the Chrome extension "Get cookies.txt LOCALLY," open `youtube.com` and confirm you're logged in, click the extension icon to export `cookies.txt` immediately, and avoid visiting YouTube again afterward to keep the cookies from getting rotated out.

**Step 2: upload the cookies to the NAS**

The cookies file has to live inside the container's mounted data directory, or the container won't be able to see it.

First confirm the container's mount path:

```bash
docker inspect <container-id> | grep -A 10 "Mounts"
```

Upload it to the right location (corresponding to `/app/data/` inside the container):

```bash
scp ~/Downloads/cookies.txt root@192.168.5.154:/volume1/docker/podsync/data/cookies.txt
```

**Step 3: edit the config file**

Add this under each feed, or in the global `[downloader]` section:

```toml
[downloader]
self_update = true
youtube_dl_args = ["--cookies", "/app/data/cookies.txt"]
```

Note: use the container-internal path `/app/data/`, not the host path.

**Step 4: restart the container**

```bash
docker restart <container-id>
```

### Things to watch for

**Cookies expire.** YouTube automatically rotates session cookies during regular browser use, which invalidates a previously exported `cookies.txt`. Under normal conditions cookies last 1–3 months, but with heavy use they can expire in as little as 2–4 weeks. The symptom is the same bot-verification error resurfacing after things were working fine, along with a log line like:

```
The provided YouTube account cookies are no longer valid. They have likely been rotated
```

When you see that, re-export and re-upload the cookies.

**Too Many Requests is IP-based rate limiting**, and it can still happen occasionally even with cookies configured. To mitigate it: increase each feed's `update_period` to reduce request frequency, and avoid having multiple containers hammer requests at the same time.

**With multiple containers deployed**, each container's config file needs to be updated individually — a batch find-and-replace command can help:

```bash
for f in config-a.toml config-b.toml config-c.toml; do
  sed -i 's|old content|new content|g' "/volume1/docker/podsync/$f"
done
```
