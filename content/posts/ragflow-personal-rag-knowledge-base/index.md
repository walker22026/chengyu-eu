---
title: "A Weekend with RAGFlow: Building My Own RAG-Based Knowledge Base"
date: 2024-08-25T16:00:30+00:00
summary: "Deploying the open-source RAGFlow project, testing it against real documents, and wiring it up to local Ollama models to keep costs at zero."
tags: ["RAG", "LLM", "Self-Hosting"]
categories: ["Blog"]
featureImage: "https://images.chengyu.eu/file/453ad3c0285f474292a95.jpg"
---

### Overview

Spent the weekend deploying the open-source RAGFlow on my home machine. Picked it partly because it supports automatic OCR out of the box; I'm also planning to try Quivr (marketed as a "second brain") and compare the two once it's installed.

### What is RAG?

**Concept:** RAG (Retrieval-Augmented Generation) combines information retrieval with a generative model to improve performance on NLP tasks — question answering and content generation in particular.

**The pieces:**

1. **LLM** — a deep neural network trained on large-scale data (GPT-4, for example), capable of generating natural language and understanding it broadly.
2. **Retrieval** — pulling information or documents relevant to the input query from an external knowledge base. This closes the gap in the LLM's own knowledge, especially for factual questions.
3. **Generation** — once relevant information has been retrieved, the model generates an answer grounded in it, combining the LLM's generative strength with facts it wouldn't otherwise reliably know.

**Workflow:** the user submits a query → the system retrieves the most relevant passages/documents from a pre-built knowledge base → the LLM generates an answer based on what was retrieved → the answer is returned to the user.

### Hands-on testing

Tried three scenarios:

1. **Q&A over a procurement contract found online.** The answer about the three-day payment window was correct. The first two clauses checked out too, but it couldn't find anything about the finance department's ¥5,000 pre-authorization — not sure if that's a hallucination or just not in the source document.
2. **Fed it a scanned PDF book and asked questions about it.** Since I hadn't read the book myself, I genuinely can't verify whether the answers were correct. Constraining it with careful prompting does seem to help avoid hallucination, though.
3. **Tried it against an 18-year-old technical standard document.** All the answers checked out. There wasn't a direct answer available in the source for one of my questions, but a capable LLM should, in principle, still be able to generate something reasonable.

### Install process

**1. Environment prep**

```
sysctl vm.max_map_count
```

If that value is low, bump it up:

```
# In this case, we set it to 262144:
sudo sysctl -w vm.max_map_count=262144
```

Setting `vm.max_map_count` higher lets a process create more memory mappings — important for workloads that need a lot of small mappings. Alternatively, edit `/etc/sysctl.conf` and add:

```
vm.max_map_count=262144
```

![Environment setup](https://images.chengyu.eu/file/453ad3c0285f474292a95.jpg)

**2. Docker install**

```
git clone https://github.com/infiniflow/ragflow.git
cd ragflow/docker
chmod +x ./entrypoint.sh
docker compose up -d
```

If you have an NVIDIA card, use `docker-compose-gpu.yml` instead:

```
docker compose -f docker-compose-gpu.yml up -d
```

If you do have an NVIDIA card but hit an error during this step, it usually means the NVIDIA Container Toolkit isn't installed:

```
sudo apt-get update
sudo apt-get install -y nvidia-docker2
```

Then restart Docker:

```
sudo systemctl restart docker
```

![Docker GPU setup](https://images.chengyu.eu/file/b2d77e7327f0893b143b1.jpg)

Once GPU support is working, the speed difference over CPU-only is dramatic.

**3. Pointing RAGFlow at local Ollama models**

Using a local model instead of a paid API brings the running cost down a lot.

1. Open the firewall port:
   ```
   sudo ufw allow 11434/tcp
   ```
2. Confirm Ollama is running: visiting <http://localhost:11434> should show "Ollama is running."
3. Check which models are installed: `ollama list`. I picked `llama3.1` and `gemma2` for RAGFlow.
4. In the RAGFlow UI: click the avatar in the top-right → **Model providers** → select Ollama. Set the model type to "chat" and add `llama3.1:latest` and `gemma2:latest`, with the URL set to `http://localhost:11434/`. Then just enable those models under chat settings.

![Configuring the model provider](https://images.chengyu.eu/file/c9bbf97a3acf2b33ae7b9.jpg)

![Adding local Ollama models](https://images.chengyu.eu/file/0dc299c4fe8d45fcd9e06.jpg)

![Testing a query](https://images.chengyu.eu/file/2a51838d73cd113b1d030.jpg)

![Retrieval results](https://images.chengyu.eu/file/7b1303de9ebc06cb1607f.jpg)

![Generated answer](https://images.chengyu.eu/file/828d57ee0d4f4a1861ba4.jpg)

![Testing against the scanned PDF](https://images.chengyu.eu/file/723be3f041b9e31c03515.jpg)

![Testing against the older standard document](https://images.chengyu.eu/file/9171bf76eacc36948c0e5.jpg)

![Final results](https://images.chengyu.eu/file/930f265a6c1b05e85462e.jpg)

![RAGFlow dashboard](https://images.chengyu.eu/file/e6d154aa85e512abbc4f1.jpg)

### Afterthoughts

Plan to gradually feed in my journal entries, blog posts, and other personal records, building it into a kind of digital record of my own history. The generation step is fairly resource-hungry, so RAG doesn't seem well-suited to high-concurrency use cases — but for documents that change often, it's still the more practical option compared to constantly re-indexing something heavier.
