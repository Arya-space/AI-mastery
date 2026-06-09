# What Is an LLM

A **Large Language Model** is a transformer-based neural network trained to predict the next token given a sequence of tokens. Trained on trillions of tokens from the internet, books, and code.

The key innovation is **self-attention**: the model dynamically weighs the importance of every other token in the sequence when predicting the next one — regardless of position. This is what makes transformers far more powerful than earlier approaches.

## Brief History

| Year | Milestone |
|------|-----------|
| 1950 | Turing test — first framework for evaluating machine intelligence |
| 1980s–90s | Statistical models (n-grams) — predict next word from word counts |
| Early 2010s | Neural networks — move beyond word order to semantic understanding |
| 2017 | **"Attention Is All You Need"** (Vaswani et al.) — transformer architecture introduced |
| 2018 | BERT (Google), GPT-1 (OpenAI) — first large pretrained transformer models |
| 2020 | GPT-3 — 175B parameters, few-shot learning capability |
| 2022+ | ChatGPT, GPT-4, Claude, Gemini — mainstream LLM era |

## How an LLM is Built (Training Stages)

By the time you call an API like Claude or GPT-4, the model has already gone through 3 stages of training. You don't do any of this — Anthropic/OpenAI do it. But knowing it exists explains *why* models behave the way they do.

**Stage 1 — Pre-training**
The model reads trillions of tokens from the internet, books, and code. It learns one simple task: predict the next word. After this it's extremely knowledgeable but not useful yet — it'll just continue text rather than answer questions.

**Stage 2 — Instruction Tuning (SFT — Supervised Fine-Tuning)**
The model is shown thousands of (question → good answer) pairs. It learns to follow instructions instead of just continuing text. This is what turns a raw model into a "chat" model.

**Stage 3 — RLHF (Reinforcement Learning from Human Feedback)**
Humans compare two model responses and pick which is better. The model is trained to prefer responses that humans rate highly — more helpful, less harmful, better formatted.

> Each of these is a deep topic in itself. Pre-training involves distributed training across thousands of GPUs. SFT is essentially supervised ML. RLHF involves reinforcement learning, reward models, and PPO — all separate ML fields. This is covered in depth in the ML repo.

> As an AI engineer: you use the finished product. You need to know this exists for interviews and to understand *why* fine-tuning and RAG work the way they do.

## How an LLM Generates Text

When you send a message to an LLM, it doesn't write the whole response at once. It generates **one token at a time**, and each token is predicted based on everything before it. This is called **autoregressive generation**.

Think of it like autocomplete — but at a much deeper level. The model isn't picking the most common next word, it's predicting the most likely next token given the full context of the conversation.

**Temperature** — controls how "creative" or "safe" the output is
- `0` = always picks the most likely token. Deterministic, repetitive, safe.
- `1` = default. Balanced randomness.
- `>1` = more random and creative, but can go off track.
- In production you usually keep this low (0–0.3) for factual tasks, higher for creative ones.

**Top-p / Top-k** — sampling strategies that limit *which* tokens the model can pick from
- Instead of choosing from all possible tokens, you restrict to the top candidates
- Prevents the model from picking very unlikely / nonsensical tokens
- These are parameters you can set in the API

> Temperature, top-p, and top-k are all things you control when calling the API. Understanding them helps you tune model behavior for your use case — not just guess-and-check.



## Key Families

**Closed / API-only:**
| Model | Provider | Notes |
|-------|----------|-------|
| GPT-4o | OpenAI | Strong general + multimodal |
| Claude 3.5/4.x | Anthropic | Strong coding, long context, safety |
| Gemini | Google | Multimodal, long context |

**Open weights (self-hostable):**
| Model | Provider | Notes |
|-------|----------|-------|
| Llama 3 | Meta | Most popular open model, strong general capability |
| Mistral / Mixtral | Mistral AI | Efficient, great for fine-tuning |
| Qwen | Alibaba | Strong multilingual |

**Specialist / encoder models (used in embeddings + classification):**
| Model | Provider | Notes |
|-------|----------|-------|
| BERT | Google (2018) | Encoder-only, bidirectional — used for classification, search |
| RoBERTa | Meta (2019) | Optimized BERT variant |
| T5 | Google | Encoder-decoder, treats everything as text-to-text |

> BERT-style models are common in RAG pipelines (as the embedding model) and fine-tuning for classification tasks.

## Parameters = Model Size
More parameters → more capacity to learn patterns. GPT-4 estimated ~1.8T parameters. Bigger ≠ always better for specific tasks.

## LLMs in Actual Development

As an AI engineer, you interact with LLMs in two main ways:

### 1. Via API (most common path)
- Call a hosted model (OpenAI, Anthropic, Gemini) over HTTP
- Pay per token — no GPU, no infrastructure
- Model is already trained and aligned
- You control behavior through prompts, system prompts, and parameters
- This is the primary approach for building AI applications

### 2. Open Source / Self-Hosted
- Run models like Llama 3 or Mistral locally or on your own server
- Tools: **Ollama** (local), **vLLM** (production serving), **HuggingFace Transformers**
- More control over data privacy and cost at scale
- Requires GPU infrastructure

### 3. Fine-tuning Open Source Models
- Take a base open model (Llama, Mistral) and train it further on your own data
- Common technique: **LoRA / QLoRA** — efficient fine-tuning without full retraining
- Use cases: domain-specific language, custom tone, classification tasks
- This is the bridge to ML engineering — covered in depth in the ML repo

> For most AI engineering work: start with the API. Move to open source when you need data privacy or cost control at scale. Fine-tune only when prompting and RAG aren't enough.


## What the Model Knows / Doesn't Know
- Knows: patterns from training data up to **knowledge cutoff**
- Doesn't know: events after cutoff, private data, real-time info
- Can hallucinate: confidently produce plausible-sounding but false information