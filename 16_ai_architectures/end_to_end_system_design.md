# End-to-End AI System Design

How all the pieces connect in a production AI system — from user request to response.

## Full System Map

```
USER
  │
  ▼
[ API Gateway / Rate Limiter ]
  │
  ▼
[ Input Guardrails ]
  ├── Length check
  ├── Moderation (toxicity, harmful content)
  ├── PII detection
  └── Prompt injection detection
  │
  ▼
[ Session / State Manager ]
  ├── Load conversation history (Redis)
  └── Load user profile / preferences
  │
  ▼
[ Context Builder ]
  ├── RAG retriever (if knowledge base exists)
  ├── Long-term memory retrieval
  └── Build final prompt
  │
  ▼
[ LLM / Agent ]
  ├── Single call → skip to output
  └── Agent loop:
        ├── LLM decides next action
        ├── Tool execution
        └── Repeat until done
  │
  ▼
[ Output Guardrails ]
  ├── Schema validation
  ├── Toxicity check
  ├── PII redaction
  └── Faithfulness check (for RAG)
  │
  ▼
[ Logger + Tracer ]
  ├── Log: tokens, latency, cost, model
  ├── Trace: full agent trajectory
  └── Eval sample: run automated eval on ~5% of traffic
  │
  ▼
USER ← Response
```

## Component Ownership

| Component | Technology Options |
|-----------|-------------------|
| API layer | FastAPI, Flask |
| Rate limiting | Redis + custom, or API Gateway |
| Input guardrails | OpenAI Moderation API, Llama Guard, custom classifier |
| Session state | Redis |
| RAG retrieval | FAISS / Chroma / pgvector / Pinecone |
| Reranking | Cohere Rerank, BGE cross-encoder |
| LLM | OpenAI, Anthropic, Gemini, self-hosted |
| Agent framework | OpenAI Agents SDK, LangGraph, custom |
| Output guardrails | Pydantic, custom classifiers |
| Logging | Langfuse, LangSmith, custom structured logs |
| Monitoring | Datadog, Grafana, Helicone |

## Latency Budget (Typical)
```
Input guardrail check:    ~50ms
Session load (Redis):     ~5ms
RAG retrieval:            ~100–300ms
Reranking:                ~100–200ms (optional)
LLM call (single):        ~500ms–3s
Output guardrail:         ~50ms
Logging (async):          ~0ms (non-blocking)
─────────────────────────────────
Total (no agent):         ~800ms–4s
Total (agent, 5 steps):  ~5–20s
```

## What to Build First (MVP vs Production)

### MVP
```
FastAPI endpoint → build prompt → call LLM → return response
```
No RAG, no guardrails, no tracing. Validate the use case first.

### Production-ready additions (in order)
1. Input validation + basic moderation
2. Structured logging (tokens, latency, errors)
3. Error handling + retries
4. RAG if needed
5. Output validation
6. Full observability (Langfuse)
7. Evals in CI
8. Human feedback loop
