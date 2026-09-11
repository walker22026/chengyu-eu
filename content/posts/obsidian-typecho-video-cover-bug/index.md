---
title: "Debugging a Broken Homepage: When a .mov File Got Treated as a Cover Image"
date: 2025-06-10T16:00:09+00:00
summary: "A cover-image extractor that grabbed the first <img> tag without checking it was actually an image — and broke the homepage whenever a post embedded video."
tags: ["Typecho", "Obsidian", "Troubleshooting"]
categories: ["Blog"]
---

### Background

I've always written my posts in Markdown inside Obsidian, paired with a script that automatically publishes the content to my Typecho blog — a seamless writing-to-publishing pipeline. The whole flow looks like:

- Write a `.md` file in Obsidian
- A script converts the `.md` into HTML (or a content fragment) and publishes it to Typecho through an API
- Once published, the homepage automatically shows the post along with its cover image

Everything worked fine, until I noticed that **some posts on the homepage had stopped showing their cover images**, and the layout was breaking in places.

### Digging in

After some investigation, I traced it to this: whenever a Markdown file in Obsidian had a video link embedded (a `.mov` file, for instance), the logic that auto-extracts a cover image would mistakenly treat the `.mov` as if it were an image. The homepage template would generate `<img src="xxx.mov" />`, which is invalid HTML, and the cover image would fail to load.

### Root cause

The theme's cover-image-extraction function just grabbed the `src` of the first `<img>` tag it found, without checking whether that source was actually an image format.

### The fix

I rewrote the function to be more robust: it now walks through every `<img>` tag's `src`, skips anything that's a `.mov`, `.mp4`, or other non-image format, and only returns once it hits the first genuine image URL.

### Result

After the fix, the homepage went back to normal — it no longer mistakes a video link for an image, and correctly skips past any video links to find the actual next image.
