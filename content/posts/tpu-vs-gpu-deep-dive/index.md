---
title: "TPU vs. GPU: A Deep Dive, and Who's Actually Using TPUs"
date: 2026-01-18T17:00:08+00:00
summary: "GPUs are the flexible all-rounder with a mature ecosystem; TPUs are the specialist that's brutally efficient at large-scale matrix math — plus a rundown of who actually trains on TPUs."
tags: ["AI", "Hardware"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/01/18/Pasted-image-20260118164849.jpg"
---

A deep dive comparing Google's TPU (Tensor Processing Unit) against NVIDIA's GPU (Graphics Processing Unit).

The short version: **the GPU is the all-around generalist, flexible with a mature ecosystem; the TPU is the specialist — brutally efficient at the specific large-scale matrix math that deep learning training relies on.**

![A look at TPU architecture](https://media.chengyu.eu/images/2026/01/18/Pasted-image-20260118164849.jpg)

### 1. Core positioning and architecture

This is the most fundamental difference between the two, and it decides what each one is actually good at.

| | **GPU** | **TPU** |
| --- | --- | --- |
| **Original purpose** | Originally built for graphics rendering, later generalized into parallel computing (GPGPU) | A custom ASIC **purpose-built for machine learning**, deep learning in particular |
| **Core architecture** | **SIMD** (single instruction, multiple data): thousands of smaller cores (like CUDA cores), good at handling many independent parallel tasks at once | **Systolic array**: built around a large matrix-multiply unit (MXU); data flows through the chip and gets reused the way blood flows through a heart |
| **Flexibility** | **Very high** — beyond AI, also handles graphics rendering, physics simulation, scientific computing; supports multiple precisions (FP64, FP32, FP16, INT8) | **Lower** — highly focused on matrix multiplication and convolution, usually optimized hard for low precision (like bfloat16) |
| **Memory access pattern** | Needs frequent reads from DRAM; bandwidth is high, but memory access is still a bottleneck | **Minimizes memory access** — once data is loaded, it flows through the array stage by stage, maximizing reuse and cutting latency and power draw |

> **An analogy:** a **GPU** is like a team of a few thousand ordinary mathematicians, each working a problem on their own sheet of paper and passing it to the next — great when everyone's doing similar work in parallel. A **TPU** is like a precision assembly line: raw material (data) goes in, moves through a fixed sequence of processing steps without being shuffled back and forth, and comes out as a finished product. Extremely fast at one specific product (matrix operations), but hard to retool for anything non-standard.

### 2. Performance and efficiency

**Training speed:** TPUs tend to beat GPUs of the same generation on large models (Transformers, BERT, ResNet) and very large batch sizes — a TPU Pod (cluster) scales to thousands of chips over a fast interconnect with excellent near-linear speedup. GPUs do better on small batches, non-standard architectures, or workloads with a lot of control flow.

**Inference:** TPUs offer very low latency and high throughput, which suits high-concurrency real-time services well. GPUs (especially inference cards like NVIDIA's T4, A10, L4) are also very capable, and their generality often makes them easier to deploy flexibly.

**Performance per watt:** TPUs win outright. Since they cut out hardware logic for things like graphics rendering, TPUs deliver more FLOPS per unit of energy, which matters enormously at data-center scale.

### 3. Ecosystem and development difficulty

This is currently the GPU's biggest moat.

**GPU (NVIDIA CUDA):** a near-monopoly ecosystem — virtually every deep learning framework (PyTorch, TensorFlow, MXNet, etc.) supports GPUs first and best. Community resources are extensive, and solutions to most problems are a search away. It also runs anywhere: your own machine, AWS, Azure, GCP, Alibaba Cloud, and more.

**TPU (Google Cloud):** tightly bound to Google's own ecosystem, mainly used through TensorFlow and JAX. PyTorch support exists via PyTorch/XLA, but compared to native CUDA support it has more rough edges and is harder to debug. It's also cloud-exclusive — there's no physical TPU card you can buy and plug into your own machine; it's only available for rent on Google Cloud Platform (or through Colab's free tier).

### 4. Cost

GPUs are expensive (especially high-end cards like the H100/A100) and heavily exposed to supply and demand — prices spike hard when they're in short supply. But for building compute locally, a GPU is the only option. TPUs from Google are usually competitively priced, and spot (preemptible) instances in particular can be very cheap — for long-running large-model training, TPUs often save a substantial amount on the cloud bill.

| **Scenario** | **Recommended** | **Why** |
| --- | --- | --- |
| Getting started / learning / research | GPU | Plenty of tutorials, PyTorch code just runs, easy to debug, broadly compatible |
| Small-scale experiments / personal projects | GPU | Flexible, no need to adapt code to the hardware |
| Large-scale model training (LLMs) | TPU (or a high-end GPU cluster) | At massive data scale, TPU's linear scaling and cost advantage stand out |
| Using TensorFlow / JAX | TPU | Native support is excellent, squeezes out full hardware performance |
| Using PyTorch and don't want the hassle | GPU | CUDA's ecosystem is the most mature, no XLA compiler compatibility issues to deal with |
| Need on-prem deployment / edge computing | GPU | TPUs aren't sold as physical hardware (aside from Edge TPU, which is its own separate thing) |

Companies currently using Google's TPUs fall roughly into two groups: tech companies and AI labs that use TPUs directly for training and inference (the most common case), and hardware vendors that participate in actually manufacturing TPUs.

### 5. Core users: the top tech giants and AI unicorns

These companies mainly rent TPU compute through Google Cloud to train their flagship models.

**Apple** — in a notable disclosure, Apple's own technical papers state that Apple Intelligence's foundation models were pretrained on Google's TPUv4 and TPUv5 clusters, rather than relying solely on NVIDIA GPUs — one of the strongest endorsements yet of TPU performance and stability.

**Anthropic** — as OpenAI's strongest competitor (the company behind the Claude model family), Anthropic is a deep TPU user. Google has invested in Anthropic and signed a large-scale cloud computing agreement with it, and Anthropic uses substantial TPUv5e and TPUv5p capacity to train frontier models like Claude 3.

**Midjourney** — the well-known AI image-generation tool's training and inference compute is mainly powered by Google Cloud TPUs, which offer strong cost-efficiency for this kind of large-scale generative workload.

**Hugging Face** — as something like the "GitHub" of the AI world, Hugging Face partners with Google to make open-source models on its platform easier to run on TPUs, and uses TPUs for optimized training.

**Character.AI** — a unicorn founded by former Google employees, relying heavily on Google Cloud TPUs to power high-concurrency, real-time conversations for millions of users.

**Google itself** — the biggest user of all. Nearly every internal product depends on TPUs: Gemini and PaLM are trained entirely on TPUs; Search uses them to understand query semantics (BERT/RankBrain); Waymo uses them for autonomous-driving data processing and model training; Google Photos and Translate use them for image recognition and real-time translation.

### 6. Enterprise and industry applications

These more traditional companies use TPU compute on Google Cloud to solve specific business problems.

**Salesforce** uses TPUs to train its enterprise AI models (Einstein). **LG AI Research**, the AI research arm of Korea's LG Group, uses TPUs to train its large multimodal model EXAONE. **Ford** partners with Google on autonomous driving and data analysis, using TPUs to accelerate simulation and computation. **Kakao Brain**, the AI division of Korean internet giant Kakao, uses TPUs to train Korean-language large models.

### 7. The supply chain: who actually manufactures TPUs?

If "who makes TPUs" means the hardware supply chain behind the chip itself, the list looks like this: **Google** handles the core architecture design (the ASIC design itself). **Broadcom** is a key partner, helping with the physical chip design, IP licensing, and coordinating with the foundry (turning the design into something manufacturable). **TSMC** handles the actual wafer fabrication — TPUv4 used TSMC's 7nm process, and the newer Trillium (TPUv6) and TPUv5p use more advanced nodes (5nm/4nm class). Some supply-chain reporting and rumors also point to Taiwanese ASIC design-service firms like Alchip and MediaTek potentially being involved in back-end design or peripheral chip work as TPU generations progress, alongside Broadcom.
