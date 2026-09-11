---
title: "Fixing a CUDA Error When Running Gemma2 in Ollama"
date: 2024-07-27T22:36:33+00:00
summary: "A one-line fix for a CUBLAS_STATUS_NOT_INITIALIZED crash: just update Ollama."
tags: ["Ollama", "LLM", "Troubleshooting"]
categories: ["Blog"]
---

**Command:**

```
ollama run gemma2:27b
```

**Error:**

```
Error: llama runner process has terminated: signal: aborted (core dumped)
CUDA error: CUBLAS_STATUS_NOT_INITIALIZED
current device: 0, in function cublas_handle at /go/src/github.com/ollama/ollama/llm/llama.cpp/ggml-cuda/common.cuh:826
cublasCreate_v2(&cublas_handles[device])
GGML_ASSERT: /go/src/github.com/ollama/ollama/llm/llama.cpp/ggml-cuda.cu:100: !"CUDA error"
```

**Fix:**

```
curl -fsSL https://ollama.com/install.sh | sh
```

Updating Ollama resolved it.
