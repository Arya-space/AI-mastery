# RAG Failure Modes

RAG has multiple independent failure points. Diagnose each layer separately.

## 1. Retrieval Failures
**Wrong chunks returned** — model gets irrelevant context.
- Cause: bad embedding model, poor chunk size, no reranking
- Fix: hybrid search, add reranker, evaluate recall@k

**No chunks returned** — query gets low similarity to all docs.
- Cause: vocabulary mismatch, out-of-domain query
- Fix: query expansion, lower similarity threshold, keyword fallback

## 2. Chunking Failures
**Context split across chunks** — answer spans two chunks, neither retrieved alone is enough.
- Cause: fixed-size chunking without overlap
- Fix: increase overlap, use semantic chunking, parent-child chunks

## 3. Embedding Mismatch
Embedding model trained on general text but used for specialized domain (legal, medical, code).
- Fix: fine-tune embedding model or use domain-specific model

## 4. Lost-in-the-Middle
Relevant chunk retrieved but ignored by LLM because it's buried in the middle of the context.
- Fix: put most relevant chunks first/last, reduce k, use reranker to sort by relevance

## 5. Hallucination Despite Good Retrieval
LLM ignores the context and uses its parametric knowledge anyway.
- Fix: stronger grounding instructions, lower temperature, faithfulness eval

## 6. Context Window Overflow
Too many chunks exceed the model's context limit.
- Fix: reduce k, rerank and keep only top-3, compress chunks before injecting

## 7. Stale Index
Documents updated but vector DB not re-indexed.
- Fix: trigger re-indexing on document update, track document versions

## Diagnostic Framework
```
Bad answer → Is it a retrieval problem or a generation problem?

Test retrieval independently:
  → Retrieve for known queries → check if correct chunks appear

Test generation independently:
  → Feed ideal chunks manually → check if LLM answers correctly
```
Never assume — isolate the layer.
