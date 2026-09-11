---
title: "Breaking Free: Batch-Migrating OnePlus Notes With Claude in Chrome"
date: 2026-03-15T14:45:00+00:00
summary: "When ad-stuffed 'phone switching' apps couldn't move 352 notes to a new Huawei phone, reverse-engineering the OnePlus cloud web app's API and scripting it through Claude's Chrome extension got the job done in 20 minutes."
tags: ["Tools", "Obsidian"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/03/15/2026-03-15-2.23.33.jpg"
---

Helped my wife switch to a Huawei phone over the weekend. I figured migrating her data would be routine, but I got completely stuck on the notes app. After going in circles for a while, I used Claude's Chrome extension to automate exporting all 352 notes in about 20 minutes. Here's the full process, in case you're facing the same kind of cross-platform notes migration.

### Background: an ordinary phone switch, an unusual snag

Huawei's own "Phone Clone" tool handled most of the migration smoothly — contacts, photos, apps. But when it came to the notes from her OnePlus phone, nothing worked: no matter what I tried, the official migration tool just couldn't move the notes over to Huawei directly.

### First attempt: the "switching assistant" apps recommended online

A search turned up a wave of "phone switching assistant" apps, all recommended on social media. I gave it a shot and downloaded three of them from the OPPO app store — the experience was rough across the board: opening one meant a screen full of ads with endless popups to close, the actual feature was buried behind layer after layer of onboarding screens, and some that claimed to support "notes migration" simply couldn't read OnePlus's note data at all.

Conclusion: that path was a dead end. Those tools were basically ad shells with almost no real capability behind them.

### The approach that worked: cloud, browser, and Claude's Chrome extension

After giving up on those apps, I went back to basics: if the phone-to-phone path wasn't working, move the data to a computer and handle it with something more flexible. The whole thing broke down into three stages.

**Step 1: sync the notes to OnePlus's cloud.** On the OnePlus phone, go to Settings → Account → Cloud Services, confirm cloud sync is turned on for Notes, and wait for everything to finish syncing (checkable from within Cloud Services). If cloud sync had never been turned on before, the first sync can take a few minutes — make sure the phone is on Wi-Fi and signed in properly.

**Step 2: open the OnePlus cloud notes in a browser.** On a computer, open the OnePlus cloud web app (the exact URL varies by account region), sign in with the same OnePlus account, and go to the Notes section to confirm every note shows up on the web. At this point you can browse through notes one by one in the browser — but manually copying a few hundred of them clearly wasn't realistic, which is where Claude's Chrome extension came in.

**Step 3: automate the batch export with Claude in Chrome.** This was the core of the whole approach — I used Claude's Chrome browser extension (Claude in Chrome), which can interact directly with the current page's content and run JavaScript.

Claude's first suggestion — screenshot each note, run OCR, convert to text — I ruled out. It's a general approach, but far too slow for 352 notes, and OCR accuracy on Chinese text isn't reliable enough anyway.

So I switched to an API-level approach instead. I opened the browser's developer tools (F12) and watched the page's network requests, which turned up the key pieces: an API for fetching the note list — the page calls an endpoint that returns JSON with every note's ID, title, and summary — and an API for fetching a single note's full content by its ID.

I fed all of that to Claude: the list endpoint's URL format and request method, the shape of the returned JSON (field names, nesting), and where the note content and its encoding lived in the response.

Once Claude confirmed the approach would work, it wrote and ran a JavaScript script in the extension that, roughly: called the list endpoint to get every note's ID, requested the detail endpoint for each one in turn to get its full content, converted each note to Markdown (handling the title, body, and timestamp fields properly), and packaged everything up for batch download to local Markdown files.

The whole thing took about 20 minutes, and all 352 notes came out successfully.

A few tips on the process: in the browser's dev tools, switch to the **Network** panel and filter for **XHR/Fetch** requests, then click a note in the list to see the matching API call; paste the request URL and a sample of the returned JSON straight to Claude and it can work out the data structure itself; if there are a lot of notes, check whether the API uses pagination parameters (`page`, `pageSize`, `offset`) and let Claude know so it loops through them; and some cloud services rate-limit their APIs, so it's worth adding a small delay between requests (500ms or so) in the script.

### Step 4: getting the Markdown notes onto the new phone

After all this, I made a decision: drop the phone's built-in notes app entirely and move to Obsidian instead.

The reasoning is simple: the data format is universal — Obsidian just uses plain Markdown files, with no proprietary format, openable in any text editor; storage is fully under your control — the files just live in a local folder, and you can sync them however you like (cloud drive, Git, a NAS); it works seamlessly across platforms — iOS, Android, Windows, macOS, Linux, all supported; and you're never locked in again — even if Obsidian shut down tomorrow, the `.md` files would still be intact and openable in any other tool.

The plan: install [Obsidian](https://obsidian.md/) on both phone and computer, drop the exported Markdown files into Obsidian's vault folder, and use Obsidian's sync options (iCloud, Remotely Save, Syncthing, etc.) to keep it available across devices.

### Wrap-up

| Step | Action | Tool |
| --- | --- | --- |
| 1 | Sync notes to the cloud | OnePlus Cloud Services |
| 2 | Log into the cloud web app to view notes | Chrome |
| 3 | Reverse-engineer the API | Browser dev tools (F12) |
| 4 | Automate the batch export to Markdown | Claude in Chrome |
| 5 | Import into a universal notes tool | Obsidian |

The core idea here is bypassing the phone's own limitations and using the cloud web app plus browser automation to pull the data out directly. In principle this isn't specific to OnePlus — it should work for any brand (OPPO, vivo, Xiaomi, and others) that offers a web version of its cloud notes service.

If you're stuck on the same notes-migration headache during a phone switch, it's worth trying this approach. And more importantly — it's a good moment to seriously consider getting your own data out of a manufacturer's walled garden for good.
