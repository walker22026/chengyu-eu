---
title: "Building a Personal Knowledge Base with MaxKB on My NAS"
date: 2024-05-10T09:00:23+00:00
summary: "Self-hosting the open-source MaxKB knowledge-base tool at home, and pointing it at a Qwen-backed model for Chinese-language documents."
tags: ["Self-Hosting", "AI", "Synology"]
categories: ["Blog"]
featureImage: "https://images.chengyu.eu/file/3e3a17499a8d7fafc54ce.png"
---

Set up a personal knowledge base today, using the open-source tool MaxKB. To keep it reachable anywhere while still being reasonably secure, I went with a self-hosted deployment on my home NAS rather than a cloud service.

### Install steps

1. Open Docker on the Synology NAS and search for the MaxKB project.
2. Download and configure it. Two things need changing: I set the port to `4444`, and created a working directory mapped to `/var/lib/postgresql/data`.
3. Once that's done, it's reachable at `http://<ip>:4444` — default username `admin`, default password `MaxKB@123..`. Change the password immediately after logging in.

![Setting up MaxKB](https://images.chengyu.eu/file/3e3a17499a8d7fafc54ce.png)

4. Create a knowledge base — I made two: one pointed at my blog's URL, which pulls in and indexes the blog's content automatically, and one built from local documents.

![Blog-backed knowledge base](https://images.chengyu.eu/file/3704a8a639c24cdea5fee.png)

![Local-document knowledge base](https://images.chengyu.eu/file/0bafc73cdf0ca0e766027.png)

5. Configure the application: pick the knowledge base from the previous step, then set the AI model. I used the OpenAI-compatible Workers AI endpoint from an earlier post, and since most of my documents are in Chinese, I picked Alibaba's Qwen model specifically. Everything else I left at its default.
6. And here's how it looks in actual use:

![MaxKB in use](https://images.chengyu.eu/file/a0361e526c2c9b8b21dcb.png)
