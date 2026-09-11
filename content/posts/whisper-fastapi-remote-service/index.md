---
title: "Wrapping Whisper in a FastAPI Service for Remote Transcription"
date: 2024-08-25T19:00:02+00:00
summary: "Offloading transcription from a GPU-less NAS to a desktop with an actual GPU, exposed as a simple internal API."
tags: ["AI", "Self-Hosting", "FastAPI"]
categories: ["Blog"]
---

### Background

Right now my NAS handles audio-to-text conversion, but without a GPU, anything more than a short clip takes hours to process. So I exposed the Whisper instance running on a desktop with a 4060 as an API service instead, and pointed the NAS at it.

### Wrapping it

Wrote a small FastAPI app that wraps the Whisper model behind an API endpoint capable of accepting an externally-uploaded audio file — loading the `medium` model and handling transcription requests through a `/transcribe/` POST endpoint.

Since it's only reachable from inside the home network, I skipped token authentication for now.
