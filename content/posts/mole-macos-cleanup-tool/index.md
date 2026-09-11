---
title: "A Simple, Solid macOS Cleanup Tool: Mole"
date: 2025-12-16T07:00:36+00:00
summary: "A restrained, transparent command-line cleaner for leftover app files and system/dev caches — scan first, then clean, nothing automatic or hidden."
tags: ["macOS", "Tools"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2025/12/15/2025-12-15-16.43.54.jpg"
---

Mole is an open-source macOS command-line cleanup tool built for clearing out system junk, leftover app files, and various caches.

It doesn't aim for "one-click clean everything automatically" — instead it's built around being visible, controllable, and safe, so you always know exactly what's on your system and what's being deleted.

Project page: [github.com/tw93/Mole](https://github.com/tw93/Mole)

### What can Mole do?

Mole keeps its feature set tightly focused, mainly around:

**1. Cleaning up leftover app files.** Uninstalling an app on macOS often leaves behind caches, config files, and logs — things like Application Support, Caches, Preferences, and Logs. Mole helps you find these leftovers, so you don't end up in the situation where you deleted the app but never got the space back.

![Scanning for leftover app files](https://media.chengyu.eu/images/2025/12/15/2025-12-15-16.43.54.jpg)

**2. Cleaning system and developer caches.** Mole supports clearing several common cache sources: system caches, Homebrew's cache, Xcode derived data, and caches from other common dev tools. It's particularly handy for developers, since it can free up a large amount of disk space quickly.

**3. Checking disk usage.** Mole can help analyze what's actually taking up space, so you find the directories and files that matter instead of cleaning blindly.

![Disk usage breakdown](https://media.chengyu.eu/images/2025/12/15/2025-12-15-16.44.23.jpg)

### Installing Mole

**Option 1: Homebrew (recommended)**

If you already have Homebrew installed, it's a single command:

```
brew install tw93/tap/mole
```

Once installed, the `mole` command is available straight from the terminal.

### Using Mole

**1. Check the help output**

```
mole -h
```

This shows every feature and command option Mole supports.

**2. Scan for what can be cleaned**

```
mole scan
```

This scans the system for common caches and leftover files and lists the results — it doesn't delete anything on its own.

![Scan results before any cleanup](https://media.chengyu.eu/images/2025/12/15/2025-12-15-16.44.38.jpg)

**3. Run the actual cleanup**

Based on the scan results, run the matching cleanup command. Mole typically confirms before it deletes anything, to avoid removing something important by accident.

### A few tips

Scan before you clean — don't run cleanup commands blindly. Running it periodically is enough; there's no need to run it constantly. And if you're not sure what a file is for, check before deleting it.

![The cleanup step in action](https://media.chengyu.eu/images/2025/12/15/2025-12-15-16.45.03.jpg)

### Wrap-up

Mole is a lightweight, restrained, trustworthy macOS cleanup tool: it doesn't run in the background, it doesn't delete things carelessly, every action is transparent and under your control, and it's fully open source. If you're comfortable in the terminal and want a cleaner way to manage space on macOS, it's well worth trying.
