# Hybrid Search

**Hybrid search** combines keyword (BM25) and semantic (dense vector) search results into a single ranked list.

## Why Hybrid?
- BM25 catches exact matches that semantic search misses
- Semantic search catches paraphrases that BM25 misses
- Combined → better recall and precision than either alone
- Standard approach for production RAG

## How It Works
1. Run BM25 search → get ranked list A
2. Run dense vector search → get ranked list B
3. Merge and re-rank using **Reciprocal Rank Fusion (RRF)**

## Reciprocal Rank Fusion (RRF)
Combines rankings from multiple lists without needing to normalize scores:
```python
def rrf_score(rank: int, k: int = 60) -> float:
    return 1 / (k + rank)

def reciprocal_rank_fusion(bm25_results, semantic_results, k=60):
    scores = {}
    for rank, doc_id in enumerate(bm25_results):
        scores[doc_id] = scores.get(doc_id, 0) + rrf_score(rank, k)
    for rank, doc_id in enumerate(semantic_results):
        scores[doc_id] = scores.get(doc_id, 0) + rrf_score(rank, k)
    return sorted(scores, key=scores.get, reverse=True)
```

## Weighted Combination (Alternative)
```python
final_score = alpha * bm25_score + (1 - alpha) * semantic_score
# alpha = 0.5 means equal weight
# alpha = 0.3 means more weight on semantic
```
Requires normalizing scores first (BM25 and cosine scores have different scales).

## Tools With Hybrid Search Built-In
- **Weaviate**: hybrid search natively
- **Elasticsearch**: BM25 + kNN combined
- **Qdrant**: supports sparse + dense vectors
- **LangChain**: `EnsembleRetriever` for combining retrievers

## When to Use Hybrid
Production RAG systems → always use hybrid. The performance gains are consistent and the implementation cost is low.
