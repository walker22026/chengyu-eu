---
title: "Fixing a Broken Cover Image in My Podcast RSS Feeds"
date: 2026-05-04T20:00:08+00:00
summary: "One podcast pipeline's cover art broke when its external image URL died; the fix was routing RSS cover URLs through a single local-file method, plus a couple of quality-of-life tweaks for a language-learning audio feed."
tags: ["Self-Hosting"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/05/04/Pasted-image-20260503075450.jpg"
---

Today I mainly dealt with a broken cover-image issue across two of my podcast-generation projects, and along the way tuned up the episode count and change-log approach for a language-learning audio podcast.

In the first project, the podcast RSS had been using an external cover image URL. Once that image's address stopped working, podcast clients could no longer display either the channel cover or the individual episode covers. This fix didn't touch audio generation, transcription, AI summarization, syncing, the player, or deployment config at all — it just switched the RSS's cover-image URLs over to a cover file stored in the local audio directory instead.

On the code side, I added a dedicated method for generating cover-image URLs, replacing the old hardcoded external image address with a single unified function call. That way, if the directory structure or access path ever changes again, there's only one place to update. After the fix, the RSS's regular channel cover, the channel's podcast cover, and each episode's cover all point to the same local cover file, removing the dependency on the dead external resource.

Once the change was in, I rebuilt and restarted the recording container and confirmed the new one was running properly. I also checked the RSS file, the cover file, and the container logs to make sure the cover URL had actually been written into the RSS and that the cover image could be read correctly by clients.

The second project is aimed at foreign-language-learning audio, turning a local audio directory into a subscribable podcast feed. I made a couple of small adjustments to it today: first, bumped the number of episodes generated per run from 50 to 80, which suits repeated, batch-style listening for study purposes better; second, added a proper cover-image configuration, filling in both the standard RSS cover field and the field Apple Podcasts uses, fixing an issue where the cover showed up blank in Apple Podcasts.

While I was at it, I also fixed a mismatch between the configured cover-file path and where the file actually lived, and added a unified method for generating media-file URLs. This method preserves directory structure and correctly handles spaces, Chinese characters, or other special characters in filenames — making future URL generation for audio, images, and other shared resources more reliable.

I also added a project changelog file to keep an ongoing record of each change. Today's entry covers the episode-count adjustment, the new cover-image configuration, filling in the RSS and Apple Podcasts cover fields, the path fix, and the corresponding container-config sync.

Overall, today's work wasn't about adding complex new features — it was about closing out the basics of the podcast-feed experience: covers no longer depend on a dead external resource, channel and episode covers stay consistent, and Apple Podcasts can now recognize and display them properly; the language-learning podcast's episode count is also better suited to daily repeat listening. The scope of the changes was fairly restrained, the core logic stayed clear, and it leaves behind a more centralized place for configuration and change tracking going forward.
