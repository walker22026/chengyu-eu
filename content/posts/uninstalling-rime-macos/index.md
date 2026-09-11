---
title: "Fully Uninstalling Squirrel (Rime) on macOS"
date: 2026-01-22T12:00:27+00:00
summary: "Going back to native macOS input plus voice dictation, and the full teardown needed since deleting the app alone leaves the input method partially resident."
tags: ["macOS", "Tools"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/01/22/2026-01-22-09.19.30.jpg"
---

### Why I'm removing it

The experience after installing it wasn't great — I still prefer the native macOS way of typing, where what I'm thinking, typing, and seeing on screen all stay in sync in one smooth motion. I'd also started using voice dictation as a complement, so I decided to remove Rime.

On macOS, uninstalling Squirrel (Rime) by just deleting the app usually doesn't clean it out completely. To avoid leftover processes auto-launching or config files interfering with the system's input behavior afterward, it's worth following a proper full removal process.

![The Squirrel input method before removal](https://media.chengyu.eu/images/2026/01/22/2026-01-22-09.19.30.jpg)

### The steps

**1. Remove Squirrel from the system's input methods**

Open **System Settings → Keyboard → Input Sources**, select **Squirrel** in the list, and click the "–" button to remove it. This stops the system from continuing to load the Squirrel input service in the background.

**2. Restart**

Once it's removed from Input Sources, restart the computer once to make sure any related processes fully quit.

**3. Delete the system-level input method app**

Open **Finder**, press Command+Shift+G, and enter the path `/Library/Input Methods`. Find and delete `Squirrel.app` in that directory.

**4. Delete the user-level Rime config**

In Finder, press Command+Shift+G again and enter `~/Library`. Find and delete the Rime config directory there — it holds the input schemas, dictionaries, and user configuration, and it's worth clearing out along with everything else during a full uninstall.

**5. Confirm after logging back in**

Log out and back in (or restart again), then confirm that Squirrel no longer appears in the system's input source list, and that no related process is still running.
