# Reranking

**Reranking** = take the top-k retrieved chunks and re-score them with a more accurate (but slower) model. Keep only the top-m for the LLM.

## Why Reranking?
Vector search uses approximate similarity — fast but imperfect.
A **cross-encoder** reranker looks at query + document together → more accurate relevance score.

```
Vector search: query embedding vs doc embedding (separate, then compare)
Cross-encoder:  query + doc together → single relevance score
```

Cross-encoders are too slow to run over all documents, but fast enough for top 20–50 candidates.

## Typical Pattern
```
Retrieve top-20 from vector DB (fast, approximate)
    ↓
Rerank top-20 with cross-encoder (slower, accurate)
    ↓
Keep top-5 for LLM context
```

## Cohere Rerank API
```python
import cohere

co = cohere.Client("your-api-key")
results = co.rerank(
    model="rerank-english-v3.0",
    query="What is the refund policy?",
    documents=["doc text 1", "doc text 2", ...],
    top_n=5
)
for r in results.results:
    print(r.relevance_score, r.index)
```

## Open-Source Rerankers
- `BAAI/bge-reranker-v2-m3` — multilingual, strong
- `cross-encoder/ms-marco-MiniLM-L-6-v2` — fast English

```python
from sentence_transformers import CrossEncoder

reranker = CrossEncoder("cross-encoder/ms-marco-MiniLM-L-6-v2")
pairs = [(query, doc) for doc in candidates]
scores = reranker.predict(pairs)
ranked = sorted(zip(scores, candidates), reverse=True)
```

## Latency / Cost Tradeoff
- Cohere Rerank: ~100–200ms for 20 docs, paid per call
- Local cross-encoder: free but needs GPU for speed
- LLM-based reranking: most expensive, use only for critical paths
