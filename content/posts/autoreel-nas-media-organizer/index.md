---
title: "AutoReel: A NAS-Oriented Tool for Auto-Organizing Movie and TV Files"
date: 2026-05-24T22:00:08+00:00
summary: "Built a small self-hosted tool that watches a download folder, identifies movies and TV episodes, and renames/moves them into an Emby/Jellyfin/Plex-friendly structure — deliberately conservative, preferring to flag unrecognized files rather than guess and rename wrong."
tags: ["Self-Hosting", "Tools"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/05/24/Pasted-image-20260524214552.jpg"
---

While organizing the movie and TV files on my NAS recently, I ran into a genuinely practical problem: filenames in the download directory are often a mess — resource-site watermarks, resolution, the release group, audio track, subtitle info, sometimes even pinyin abbreviations, alternate titles, and typos mixed in. Sorting all of that by hand is not only slow, it's also easy to get wrong.

With AI-assisted coding now part of my workflow, I've also cut back on reusing existing open-source code for this kind of thing — mostly to avoid security concerns, since it needs to run on the same NAS that holds all my other data.

So I built a small tool: **AutoReel**.

Its goal is simple: watch an input directory, automatically identify movies and TV episodes, then move and rename the files into a directory structure that Emby / Jellyfin / Plex are happy with.

Project repo:

```
https://github.com/walker22026/AutoReel
```

Writing this ended up taking far longer than I originally expected. Scaffolding the framework was easy; getting it to actually run and be usable — let alone pleasant to use — turned out to be a different story.

The biggest headache was that most of my content sources come from Aliyun Drive or Baidu Cloud shares, and to dodge takedowns, these shared resources get renamed in all sorts of mangled variations that are basically impossible to guess correctly. I tried having an LLM infer the real title from the filename, but the results weren't good. In the end I went with a deliberately simplified approach: auto-process whatever can be reliably auto-processed, and drop anything unrecognized into a dedicated folder for a human to handle later — avoiding the kind of chaos that letting an LLM auto-process everything could cause.

### What AutoReel is (and isn't)

AutoReel isn't a complex media-library management system, and it doesn't replace Emby, Jellyfin, or Plex.

It does exactly one thing: take the media files sitting in a download directory and organize them into a standard media-library structure.

For example:

```
Input directory:
/emby/source/Venom.The.Last.Dance.2160p.DV.HDR (2024)/xxx.mp4

After organizing:
/emby/Movies/Venom The Last Dance (2024)/Venom The Last Dance (2024).mp4
```

TV episodes get organized similarly:

```
/emby/TV Shows/Low IQ Crimes (2026)/Season 01/Low IQ Crimes - S01E01.mp4
```

### Current processing strategy

Once started, AutoReel watches the input directory. The current design has three core directories:

```
Input directory:  /host/emby/source
Movies directory:  /host/emby/Movies
TV directory:      /host/emby/TV Shows
```

Under the input directory, it also auto-generates:

```
_unrecognized     unrecognized items
_duplicates       duplicate files
_pending_delete   items with no media files, pending cleanup
```

The overall flow:

```
Input directory
  ├── A single video file
  │     ├── Recognized successfully -> moved to Movies/TV directory
  │     └── Recognition failed -> moved to _unrecognized
  │
  ├── A subdirectory
  │     ├── Treated as one batch
  │     ├── All videos recognized -> whole directory organized
  │     ├── Any video fails recognition -> whole directory moved to _unrecognized
  │     └── Target file already exists -> whole directory moved to _duplicates
  │
  └── _unrecognized / _pending_delete
        └── skipped during scans
```

### Why not hard links

I initially considered using hard links, so the download directory and the media-library directory could each "look" like they had an independent copy of the file, while actually only occupying one copy of disk space.

In the end, though, I went with a direct `move` mode: literally moving and renaming the original file. My use case leans toward pure media-library organization — I don't need to keep seeding, and I don't want a file appearing to exist in multiple places at once. It's simpler logic and easier to reason about: once a file is organized, it no longer exists in the source directory.

### Movie handling rules

Movies are handled in two ways.

**Single-video directories:** if a directory contains only one video file, the directory name is used to identify the movie first. For example, `Venom.The.Last.Dance.2160p.DV.HDR (2024)/xxx.mp4` gets cleaned up into "Venom: The Last Dance" and "2024," then looked up via TMDB and organized as `Movies/Venom The Last Dance (2024)/Venom The Last Dance (2024).mp4`.

**Multi-video directories:** if a directory contains multiple video files, it's more likely a movie collection or franchise — for example, an "Indiana Jones" folder with three different films inside. In that case the directory name alone isn't used to match a single movie; each video file is identified individually. Only once every file in the directory has been successfully identified does the whole batch get moved. If any single file can't be identified, the entire directory gets moved to `_unrecognized`, to avoid a half-finished directory structure.

### TV episode handling rules

TV shows follow a "directory-first" principle.

The reason: a lot of episode filenames are extremely bare, like `01 4K.mp4`, `02 4K.mp4`, `03 4K.mp4` inside a folder named after the show. Feeding `01 4K.mp4` directly into a TMDB search could easily misidentify it as some unrelated movie. That's a genuinely dangerous failure mode.

So the current strategy is: the directory name is responsible for identifying the show itself, and the filenames are only used to extract episode numbers — never searched as if they were movie titles on their own. If an episode number can't be extracted from the filename, the whole directory goes into `_unrecognized` rather than risking an incorrect rename.

### Filename cleanup

Filenames from domestic resource sites are often packed with junk, things like site watermarks, "collect and bookmark us," resolution tags, REMUX/Dolby Vision/audio-track labels, embedded-subtitle notes, and so on. AutoReel does local cleanup first, stripping out these common watermarks and technical tags before the cleaned-up title is submitted to TMDB for lookup.

### Year matching

The release year is an important safeguard against misidentification. If a filename includes a year like "(2019)," the TMDB result has to match that year (with a small tolerance, like 2018/2020). If the only search result is a same-titled movie from a completely different year, it won't be treated as a match — this avoids the classic "same title, wrong year" mixup.

### Alias mechanism

Some movies and shows have inconsistent Chinese titles, English titles, or informal translated names. AutoReel supports an alias table at `/config/aliases.json`, for example mapping a Chinese title to its official English title. The alias table takes priority over a normal search, which is useful when TMDB can't find a match, the Chinese title isn't standardized, or the filename has been heavily abbreviated.

### Why I dropped the LLM fallback

At one point I tried adding an LLM as a fallback identification method, hoping it could infer titles from messy filenames. Testing showed the results weren't reliable enough. The reasons: movie/TV identification depends heavily on matching TMDB's actual data; a lot of obscure titles, informal translations, and resource-site abbreviations fall outside what an LLM reliably knows; an LLM can produce something that "looks plausible" without actually being correct; and for automated file renaming specifically, a wrong guess is far more costly than simply failing to identify something.

So the LLM fallback was removed. The current principle: better to fail and land in `_unrecognized` than to confidently rename a file incorrectly — and that principle ended up guiding most of the project's later refinement.

### Handling duplicates

If the target file already exists, AutoReel won't overwrite it. If a newly scanned directory maps to a target path that's already occupied, the whole directory gets moved to `source/_unrecognized/_duplicates/`, along with a text file explaining why — so it can be reviewed manually to decide whether it's a redundant download, a different version worth keeping, or safe to delete.

### Manually handling unrecognized items

Anything that fails recognition lands in `source/_unrecognized`, along with an explanatory text file describing why. Handling it manually is simple: go into `_unrecognized`, fix the file or directory name, move it back into the input directory, and AutoReel will re-scan and process it. This keeps a manual fallback available without letting the program blindly process things it isn't confident about.

### Special handling for Synology NAS

On Synology, directories can contain `@eaDir`, a Synology-specific extended-attribute folder. AutoReel skips these, so they don't get swept into the media library as if they were content. It also skips `.DS_Store` and `__MACOSX`.

### Docker deployment

The project deploys via Docker, with environment variables for the watch/movie/TV directories, the unrecognized/duplicate/pending-delete folder names, the TMDB API key and language, the file action mode (move), dry-run toggle, scan-on-start, a quiet period before processing, and a minimum file size threshold.

### The role of dry run

On a first run, it's worth enabling dry-run mode, where AutoReel only prints out its plan without actually moving any files — so you can confirm the identification results look right before flipping it to actually move files.

### Current known limitations

AutoReel is still a lightweight tool and isn't trying to cover every media-management scenario. Known limitations right now: it depends on TMDB, so the NAS needs access to `api.themoviedb.org`; obscure titles, inconsistent translated names, or heavy abbreviations may still need the alias table; multiple versions of the same movie (director's cut, extended edition, etc.) aren't separately version-managed yet; raw disc directories, ISO, and BDMV structures don't have full special handling yet; and when a USB external drive is used as the input directory, the watch mechanism may interfere with the drive's sleep behavior — this needs more real-world testing on NAS hardware.

### Guiding principles

A few principles emerged over the course of building this: automation must never come at the cost of incorrect organization; the messier a filename is, the more conservative the handling should be; a failed identification can be fixed manually, but a wrong identification is far more trouble; a TV directory should never use a single episode's bare filename to search for a movie; for NAS users, Docker deployment and a low setup bar really matter; and configuration should stay centralized in `docker-compose.yml` rather than depending on a web UI or extra config files.

### Wrap-up

AutoReel solves a small but genuinely practical problem: download directories are messy, and a media library needs to be clean. It's not a replacement for a full media-library system, but it works well as an auto-organizing layer between a download directory and a media-library directory.

Right now it already handles automatic movie identification, batch TV organization, filename watermark cleanup, TMDB matching, year validation, alias mapping, duplicate isolation, a manual fallback for unrecognized files, Docker deployment, and skipping Synology-specific directories. Directions worth exploring further include better movie-version identification, stronger raw-disc-directory support, a friendlier unrecognized-items list, a lower-wake external-drive watch mode, and better alias-table maintenance.
