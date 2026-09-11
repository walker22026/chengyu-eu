---
title: "Fine-Tuning Your Own Model with Free GPU Compute"
date: 2024-05-10T17:00:18+00:00
summary: "After AMD's ROCm ecosystem let me down for local fine-tuning, free Colab GPUs turned out to be the pragmatic way to fine-tune Llama 3 for free."
tags: ["LLM", "AI", "Fine-Tuning"]
categories: ["Blog"]
featureImage: "https://images.chengyu.eu/file/9b5fe39e901f9ca6b0e12.png"
---

### Backstory

I hit a wall trying to set up local fine-tuning. I'd bought an AMD card for the price-to-performance ratio, and it's fine for running models that are already trained — but trying to set up a local fine-tuning environment is where things fell apart:

1. AMD's software ecosystem lags well behind NVIDIA's. CUDA is genuinely painless to get running, practically foolproof. ROCm, by contrast, threw error after error during install, and tracking down fixes was a slog — made worse by the fact I was also on a fresh Ubuntu 24.04 install, so there wasn't much prior art to lean on.
2. After finally fighting my way through the install, I discovered AMD's own site doesn't even list ROCm support for my consumer-grade card.
3. So I looked at NVIDIA card prices again — and they're well outside my budget.

After all that, I decided to fine-tune using cloud compute instead. Of the options out there, I went with Google Colab — mainly because it's free, which settled every other consideration.

Giving up on local deployment turned out to be a relief: cloud-based fine-tuning meant I could still produce my own "customized" model, entirely for free — as long as your data doesn't involve anything private or sensitive.

### Deployment steps

**1. Open the unsloth project**

<https://github.com/unslothai/unsloth>

Pick Llama 3 for training, and it walks you straight into running the process on Google Colab.

**2. Pick a GPU type**

The free tier's T4 GPU is enough — 15GB of VRAM.

![Picking a GPU on Colab](https://images.chengyu.eu/file/9b5fe39e901f9ca6b0e12.png)

**3. Follow unsloth's steps one by one**

At this point you need to swap in your own training set.

![Following the unsloth notebook](https://images.chengyu.eu/file/dbd833d127b881d4e1779.png)

The training data needs to be in a specific question-answer format — you can use ChatGPT or a Python script to convert your own question bank or text into this shape. Once you've generated the JSON file, upload it to <https://huggingface.co> and swap that link into the Colab notebook.

![Training data format](https://images.chengyu.eu/file/09839c306f435a02031e0.png)

Then just continue running the rest of the notebook.

![Continuing the training run](https://images.chengyu.eu/file/b898d0d3bf3aecfdeec21.png)

### Testing the result

After training, I asked it "who are you?" — testing with a new model each time — and it could already handle the question in more than three languages.

![Model responding after fine-tuning](https://images.chengyu.eu/file/90bced9d9fa5a2d979ba8.png)

You can see the fine-tuned model already handles domain-specific questions competently.

![A more detailed answer](https://images.chengyu.eu/file/67aae90b427c7fe724bc2.png)

That's a genuinely solid answer — I'd bet this model could pass a professional certification exam at this point.
