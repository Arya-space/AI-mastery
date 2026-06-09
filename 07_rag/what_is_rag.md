# What Is RAG

**RAG (Retrieval-Augmented Generation)** = retrieve relevant documents → inject into prompt → LLM generates a grounded answer.

## The Problem RAG Solves
LLMs have a **knowledge cutoff** — they don't know about:
- Your private documents
- Recent events
- Company-specific data

RAG gives the model access to external knowledge **at inference time**, without retraining.

## Core Idea
```
User query
    ↓
Search your document store
    ↓
Get top-k relevant chunks
    ↓
Inject chunks into prompt
    ↓
LLM generates answer grounded in those chunks
```

## RAG vs Fine-tuning
| | RAG | Fine-tuning |
|-|-----|-------------|
| Knowledge update | Instant (just update docs) | Requires retraining |
| Private data | Yes | Yes |
| Verifiable sources | Yes (citations) | No |
| Cost | API calls + vector DB | Training compute |
| Best for | Dynamic, updatable knowledge | Style, format, behavior |

## Where RAG Fits
- Q&A over documents, PDFs, knowledge bases
- Customer support with product documentation
- Legal / medical doc search
- Any system where you need the model to cite sources

## The Basic Pipeline
1. **Index**: load docs → chunk → embed → store in vector DB
2. **Query**: embed query → retrieve top-k chunks → inject into prompt → generate

RAG doesn't make the model smarter — it gives it better information to work with.
