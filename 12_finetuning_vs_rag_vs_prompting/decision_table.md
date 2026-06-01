# Decision Table — Prompting vs RAG vs Fine-tuning vs Agents

## The Master Decision Table

| Situation | Approach |
|-----------|----------|
| General task, model already knows | Prompting only |
| Need private/proprietary knowledge | RAG |
| Knowledge changes frequently | RAG |
| Data too large for context | RAG |
| Need consistent output format | Prompting + structured output |
| Prompting can't achieve consistent format | Fine-tuning |
| Domain-specific style/tone | Fine-tuning |
| Multi-step task requiring decisions | Agent |
| Fixed multi-step process | Workflow (not agent) |
| Best of all: style + fresh knowledge | Fine-tune + RAG |

## Decision Flow
```
Can prompting alone solve it?
    YES → Use prompting (cheapest, simplest)
    NO ↓

Does the model lack knowledge?
    YES → RAG (keep model, add data)
    NO ↓

Does the model have wrong behavior/style?
    YES → Fine-tune
    NO ↓

Does the task require autonomous multi-step actions?
    YES → Agent
    NO → Rethink requirements
```

## Cost / Complexity Order (cheapest to most expensive)
1. Zero-shot prompting
2. Few-shot prompting
3. RAG (add vector DB + embedding cost)
4. Fine-tuning (training cost + custom model serving)
5. Fine-tuning + RAG
6. Agents (multiple LLM calls per task)

## Key Insight
Start simple. Measure. Only add complexity when the simpler approach measurably fails.
