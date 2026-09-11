---
title: "Optimizing OpenClaw's Quiz Response Speed"
date: 2026-03-15T00:04:00+00:00
summary: "Adding a quiz question was taking 2-3 minutes and getting slower over time — the fix was switching the data file from a JSON array to NDJSON so new items append in one line instead of a full rewrite."
tags: ["Self-Hosting", "OpenClaw"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/03/14/Pasted-image-20260314234400.jpg"
---

**Date:** March 14, 2026 · **Problem:** adding a new quiz question was taking over 2 minutes to respond.

### The symptom

Lately, sending OpenClaw a "add to quiz" command left the bot sitting in a "typing" state for a long stretch — 2 to 3 minutes before it would respond — and the delay kept getting worse as the quiz grew.

### Root cause

**The data layout.** Digging in, I found the quiz data was split across two files: `quiz_state.json` (56KB, 225 entries) — the actual practice engine, holding SRS data like `totalCorrect`/`totalWrong`/`weight` — and `pt_learning_quiz.json` (77KB, 224 entries) — a backup of the original question bank with detailed explanations, categories, and tags. Practice sessions only ever read `quiz_state.json`; the other file sat essentially unused.

**Why it was slow.** `quiz_state.json` was stored as a standard JSON array. Every time a question got added, the agent had to read the entire file (56KB, 15,000+ tokens), parse the whole array, append the new entry, and write the whole thing back. The bigger the file got, the more tokens each operation burned, and the slower it got.

**Extra overhead from duplicate checking.** The existing rules required checking for a duplicate ID before adding anything new, which meant scanning every entry — adding even more time to each request.

### Fixes

**Turning off duplicate checking.** Added a rule to `memory/quiz_rules.md`:

```
Bulk Addition: Skip duplicate checking and verification during additions.
Simply append new items to the records.
```

Result: additions skip the full scan now, but still require a full read/write of the file.

**Migrating to NDJSON (the core fix).** Converted `quiz_state.json` from a standard JSON array to NDJSON — one independent JSON object per line:

```
{"id":"verb_chegar...","question":"...","srs":{...}}
{"id":"sentence_ele...","question":"...","srs":{...}}
{"id":"sentence_eu...","question":"...","srs":{...}}
```

The original file was backed up as `quiz_state.json.bak` (56KB); the migrated file came out at 44KB — 12KB smaller, with all 225 entries intact.

Added rules 8 and 9 to `quiz_rules.md` to document the new format:

```
8. Storage Format: NDJSON, one independent JSON object per line
9. Operations:
   - Add: append a new line directly (no need to read the existing file)
   - Update: read the full file → edit the matching line → write it back
```

### Before and after

| Operation | Before | After |
| --- | --- | --- |
| Adding a question | Read the full 56KB file + edit the end + write it back | Append one line directly ✅ |
| Token cost (adding) | 15,000+ tokens | Under 100 tokens ✅ |
| Updating SRS after answering | Read full file + edit + write back | Read full file + edit + write back (no change) |
| File size | 56KB | 44KB |

### What's left unoptimized

**Updating SRS after answering still can't be sped up** — every answer needs to update a specific entry's `totalCorrect`/`totalWrong`, and NDJSON still requires a full read/write since there's no way to edit a single line in place. With 225 entries it's not a big deal right now; if it grows into the thousands, migrating to SQLite is worth considering.

**`pt_learning_quiz.json` stays as a backup** — I'm not folding its explanation field into `quiz_state.json`, to avoid bloating that file further.

### Files involved

```
/root/clawd/memory/quiz_state.json        # NDJSON format, currently in use
/root/clawd/memory/quiz_state.json.bak    # backup of the original JSON array format
/root/clawd/memory/pt_learning_quiz.json  # original question bank backup (unused)
/root/clawd/memory/quiz_rules.md          # SRS rules file (updated)
```
