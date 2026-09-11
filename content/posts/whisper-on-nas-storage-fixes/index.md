---
title: "Deploying Whisper on My NAS: Two Storage Gotchas"
date: 2024-08-10T21:00:17+00:00
summary: "A simple script to auto-transcribe Obsidian voice notes, derailed twice by the NAS running out of space in root and /tmp."
tags: ["Self-Hosting", "AI", "Synology"]
categories: ["Blog"]
featureImage: "https://images.chengyu.eu/file/dae3999c22e14429b6ca4.jpg"
---

### Background

Today's project was simple: take the script I'd just written and use it to automatically transcribe audio files from Obsidian's Whisper folder into Markdown text. The code itself was a light adaptation of the sample Python script from Whisper's own docs — nothing complicated. Installing Whisper on the NAS, though, was a different story: mostly a string of errors caused by running out of space under `root` and `/tmp`.

### Environment install issues

**1. Create a virtual environment**

```
cd /volume1
python3 -m venv whisper_env
source whisper_env/bin/activate
```

**2. Point pip's cache/temp dirs elsewhere**

```
mkdir -p /volume1/tmp/pip_cache
XDG_CACHE_HOME=/volume1/tmp/pip_cache TMPDIR=/volume1/tmp pip3 install openai-whisper
```

Installing this way finally succeeded.

![Environment setup](https://images.chengyu.eu/file/dae3999c22e14429b6ca4.jpg)

### Runtime issue

When I first tried actually running it, I hit an out-of-space error again — this time because the program needs to download the Whisper model itself, and the NAS didn't have room. Fixed it by symlinking Whisper's cache directory over to a volume with more space:

```
# Make sure the target directory exists
mkdir -p /volume1/whisper_cache

# Remove the old cache directory, if present
rm -rf /root/.cache/whisper

# Symlink /root/.cache/whisper to /volume1/whisper_cache
ln -s /volume1/whisper_cache /root/.cache/whisper
```

![Runtime fix](https://images.chengyu.eu/file/e77f73b4e822acd1deaeb.jpg)

After that, it ran fine — automatically transcribing voice notes from Obsidian.

![Working transcription](https://images.chengyu.eu/file/18d155f851be955c7aaba.jpg)
