# What Is an LLM

A **Large Language Model** is a transformer-based neural network trained to predict the next token given a sequence of tokens. Trained on trillions of tokens from the internet, books, and code.

## Training Stages
1. **Pre-training** — predict next token on massive text corpus; learns grammar, facts, reasoning patterns
2. **Instruction tuning (SFT)** — fine-tuned on (prompt, ideal response) pairs to follow instructions
3. **RLHF** — humans rank outputs; model learns to prefer human-preferred responses

## Text Generation
- **Autoregressive**: generates one token at a time, each prediction conditions on all previous tokens
- **Temperature**: controls randomness (0 = deterministic, 1 = default, >1 = creative/chaotic)
- **Top-p / Top-k**: sampling strategies to avoid low-probability nonsense

## What the Model Knows / Doesn't Know
- Knows: patterns from training data up to **knowledge cutoff**
- Doesn't know: events after cutoff, private data, real-time info
- Can hallucinate: confidently produce plausible-sounding but false information

## Key Families
| Model | Provider | Notes |
|-------|----------|-------|
| GPT-4o | OpenAI | Strong general + multimodal |
| Claude 3.5/4.x | Anthropic | Strong coding, long context, safety |
| Gemini | Google | Multimodal, long context |
| Llama 3 | Meta | Open weights, self-hostable |

## Parameters = Model Size
More parameters → more capacity to learn patterns. GPT-4 estimated ~1.8T parameters. Bigger ≠ always better for specific tasks.
