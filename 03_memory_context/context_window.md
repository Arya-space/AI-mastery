# Context Window

The **context window** is the maximum number of tokens the model can process in a single call — input + output combined. It's the model's working memory. Nothing outside it exists to the model.

## Input vs Output Tokens
- **Input (prompt)**: processed in parallel (fast, cheap)
- **Output (completion)**: generated sequentially (slow, expensive ~3–5× per token)

## Current Window Sizes
| Model | Context |
|-------|---------|
| Gemini 1.5 Pro | 1M–2M tokens |
| Claude 3.5/4.x | 200K tokens |
| GPT-4o | 128K tokens |
| Llama 3 | 8K–128K tokens |

## Context Management Strategies
- **Truncation**: drop oldest messages. Simple but causes amnesia.
- **Summarization**: compress old history into a summary. Loses details.
- **Sliding window**: keep only last N tokens. Used in chat apps.
- **RAG**: don't put everything in context — retrieve only relevant chunks.

## Lost-in-the-Middle Problem
LLMs pay most attention to the **beginning** and **end** of context. Info buried in the middle gets ignored.

**Mitigation**: put system instructions first, user question last, documents in the middle.

## Design Impact
- Small context → need RAG
- Large context → can stuff full docs, but costs more and has attention degradation
- Always track token usage to control cost and latency
