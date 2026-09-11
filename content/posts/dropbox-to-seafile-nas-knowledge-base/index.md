---
title: "From Dropbox to Seafile: Letting the Files on My NAS Slowly Grow Into a Knowledge Base"
date: 2026-06-15T08:00:48+00:00
summary: "After bringing photos home to Immich, files were next — Seafile replaced Dropbox for syncing, then got paired with Open WebUI, RAG, and LiteLLM to turn a plain file store into a searchable, AI-queryable knowledge base."
tags: ["Self-Hosting", "AI"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/06/14/Pasted-image-20260615074756.jpg"
---

### Foreword

For years, Dropbox was the single most important platform for managing and syncing my files — work material, study notes, family documents, and various project files all relied on it to stay in sync across devices. This past weekend, I'd just finished an important piece of work: using Immich to replace Google Photos, bringing my photos under unified, self-hosted management. Once the photos had migrated, a new question naturally came up — if the photos had come home to my own NAS, shouldn't my files too? So I started looking for a Dropbox alternative. After a round of research and testing, I settled on Seafile. And, more interestingly, along the way I discovered Seafile could do more than just replace Dropbox's syncing — it could be paired with AI capability to gradually build out a real knowledge base.

### Why Seafile

Within the NAS ecosystem there's no shortage of file-sync options — Synology Drive, Nextcloud, OwnCloud, Resilio Sync, Seafile. I ended up going with Seafile, mainly for three reasons.

**Excellent sync performance.** Dropbox's biggest strength has always been syncing, and Seafile is one of the few open-source options that comes close to that experience. It uses block-level sync — only the changed parts of a file get uploaded — which makes large-file syncing efficient, uses network bandwidth better, and stays stable across multiple devices. In actual use, the sync experience fully met my expectations.

**Data stays in my own hands.** Dropbox is, at its core, still a SaaS service — convenient, but the data ultimately lives on a third party's platform. Seafile runs on my own NAS: full control over the data, no subscription storage limits, the ability to layer on additional backups through the NAS itself, and no exposure to future platform policy changes. This lines up exactly with the same logic behind choosing Immich over Google Photos.

**Lower resource usage.** My hardware is a Synology DS218+ with 10GB of RAM. Compared to an all-in-one platform like Nextcloud, Seafile focuses specifically on file syncing itself, which means simpler deployment, lower resource usage, and stable long-term operation — very friendly to an older NAS.

### From file syncing to knowledge management

After using it for a while, I ran into an issue: Seafile handles file syncing well, but it doesn't solve a different problem — how do you quickly find the knowledge buried inside your files? As the material kept piling up — project plans, meeting notes, study notes, technical docs, blog material — it was all safely stored in Seafile, but actually finding a specific piece of content still meant recalling a filename, recalling a directory structure, or manually searching.

Meanwhile, Dropbox itself has gradually evolved toward a smart content platform in recent years — AI search, content summarization, smart Q&A, document linking — capabilities that the Seafile community edition simply doesn't have.

So I started wondering: could Seafile just focus on storage, while the AI capability gets bolted on externally? That led to the architecture below.

### Seafile + Open WebUI + RAG

The overall architecture:

```
Seafile
    │
    ▼
Seaf-CLI real-time sync
    │
    ▼
Local NAS directory
    │
    ▼
oikb auto-sync
    │
    ▼
Open WebUI Knowledge
    │
    ▼
LiteLLM
    │
    ├── Gemini Flash (Q&A)
    └── Gemini Embedding (vectorization)
```

The overall idea is quite simple:

**Layer one: Seafile.** Responsible for file storage, syncing, and version management. No AI functionality lives at this layer.

**Layer two: Seaf-CLI.** This is the most critical step in the whole setup. Open WebUI can't read Seafile directly, so I used the official `seaf-cli` client to sync Seafile libraries into a local NAS directory in real time. Once synced, that directory is just a normal folder — `/work`, `/home`, `/study`, `/private` — and every file change gets picked up automatically.

**Layer three: Open WebUI.** Handles document parsing, vector storage, RAG retrieval, and AI conversation. No local model here — it connects to the already-deployed LiteLLM gateway instead.

**Layer four: LiteLLM.** Manages all the models in one place. Current setup: a Q&A model, `gemini-2.5-flash`, responsible for generating the final answers; and an embedding model, `gemini-embedding-001`, responsible for vectorizing documents and questions and doing similarity search — an essential layer for building a real knowledge base.

### Automatic incremental sync into the knowledge base

If every new file had to be manually uploaded into the knowledge base, this whole setup would lose its point. So I brought in oikb (Open WebUI Knowledge Base Sync), which handles automatically scanning the directory, ingesting new files automatically, updating modified files automatically, cleaning up deleted files automatically, and running incremental syncs on a schedule. That closes the loop end to end:

```
File placed into Seafile
        │
        ▼
Auto-synced to NAS
        │
        ▼
Open WebUI auto-updated
        │
        ▼
Vectors auto-generated
        │
        ▼
Knowledge base updates in real time
```

The whole process requires no manual intervention.

### How it actually works in practice

Once deployed, the biggest change is that I no longer rely on directory structure to find material — I just ask my own knowledge base directly. For a work-related question, something like "summarize the progress on the customer-service agent project over the past three months." For a personal one, "what did I previously write about the Immich migration?"

### Where things stand now

As of now, Seafile has basically met my syncing needs in place of Dropbox. But if you only look at syncing ability, it's still just a file-management tool. What actually showed me its real value was pairing it with AI. I've increasingly come around to a particular way of thinking: storage and intelligence should be decoupled. Seafile handles saving the data. Open WebUI handles understanding the data. LiteLLM handles connecting the models. That way, each layer can evolve independently — even if Seafile eventually gets swapped for a different sync platform, or Gemini gets swapped for Claude, or Open WebUI gets swapped for a new RAG system, the overall architecture still holds.

### Closing thoughts

Migrating from Google Photos to Immich, then from Dropbox to Seafile, has completed two important steps in taking back my personal data: photos back under my own control, files back under my own control. And now a third step is underway: making that data genuinely valuable as knowledge. Seafile is still in a trial phase for me right now, and I haven't fully decided whether it'll completely replace Dropbox, since there's still some gap between the two when it comes to intelligent document management. But as Open WebUI, RAG, and large-model capability keep improving, I find myself looking forward to a future where files just sit there in storage while the knowledge inside them gets actively surfaced, searched, and put to use. Maybe the day I no longer need to remember where a file is — and can just ask my own data a question instead — is the day this whole setup will really be finished.
