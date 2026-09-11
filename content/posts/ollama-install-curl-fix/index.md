---
title: "Fixing an Ollama Install Failure Caused by a Snap-Installed curl"
date: 2024-08-03T00:00:31+00:00
summary: "Installing curl via snap put it ahead of /usr/bin on PATH, which broke the Ollama installer in a confusing way."
tags: ["Ollama", "Ubuntu", "Troubleshooting"]
categories: ["Blog"]
---

### The problem

Running Ollama's official install script failed with `curl` not found. Following Ubuntu's suggestion, I installed curl via `snap`. After that, re-running the Ollama installer failed again — this time with a "directory does not exist" error, and the install wouldn't complete.

### The fix

Don't install curl via snap. Once snap's version of curl is on the `PATH`, reinstall curl properly instead:

1. **Update the package list:**
   ```
   sudo apt update
   ```
2. **Install curl via apt:**
   ```
   sudo apt install curl
   ```
   This is the right way to do it — `apt` is Ubuntu's default package manager.
3. **Verify the install:**
   ```
   curl --version
   ```
   This should print the installed curl version.

To make sure the `/usr/bin` version of curl takes priority, edit `~/.bashrc` and reload it:

1. **Edit `~/.bashrc`:**
   ```
   nano ~/.bashrc
   ```
2. **Add this `PATH` setting at the end of the file:**
   ```
   export PATH=/usr/bin:/usr/local/cuda-12.4/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
   ```
3. **Save and exit.**
4. **Reload it:**
   ```
   source ~/.bashrc
   ```

That resolved the curl-related install failure — from there, the official Ollama installer ran through cleanly.
