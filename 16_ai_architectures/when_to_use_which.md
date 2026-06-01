# When to Use Which Architecture

## Decision Framework

```
What does your system need to do?
│
├─ Answer a question with known info the model has?
│   → Single LLM call
│
├─ Answer from your own documents / private data?
│   → RAG
│
├─ Multi-step processing with fixed steps?
│   → Prompt chain / workflow
│
├─ Complete an open-ended task requiring tool use?
│   → Tool-using agent
│
├─ Complex task spanning multiple domains?
│   → Multi-agent
│
└─ Any of the above + irreversible actions?
    → Add human-in-the-loop
```

## Complexity vs Reliability Tradeoff
```
Single call  →  Chain  →  RAG  →  Agent  →  Multi-agent
   Simple                                      Complex
   Reliable                                    Less reliable
   Cheap                                       Expensive
   Fast                                        Slow
```

More complexity = more capability but more failure modes. Only add complexity when simpler patterns measurably fail.

## By Use Case

| Use Case | Architecture |
|----------|-------------|
| Text summarization | Single LLM call |
| Sentiment classification | Single LLM call |
| Q&A over company docs | RAG |
| Customer support bot | RAG + tool calling |
| Code review pipeline | Prompt chain |
| Research assistant | Tool agent |
| Data analysis + report | Tool agent or chain |
| Multi-domain support bot | Multi-agent with handoffs |
| Autonomous coding agent | Tool agent + human loop |
| Financial transaction agent | Tool agent + human loop |
| Content moderation | Chain with guardrails |

## Cost Guide
| Pattern | LLM calls per user request |
|---------|--------------------------|
| Single call | 1 |
| Chain (3 steps) | 3 |
| RAG | 1–2 (embed + generate) |
| Agent (5 tool calls) | 6–10 |
| Multi-agent (3 agents) | 15–30+ |

Multi-agent systems can get expensive fast. Always set max iteration limits and monitor costs.

## Reliability Guide
| Pattern | Failure modes |
|---------|--------------|
| Single call | Hallucination, wrong format |
| Chain | Error propagation (step 1 fails → all fail) |
| RAG | Retrieval failure, hallucination despite good retrieval |
| Agent | Loops, wrong tool calls, prompt injection |
| Multi-agent | All agent failures + handoff context loss |

**Rule**: start with the simplest architecture that could work. Add complexity only when you have evidence it's needed.
