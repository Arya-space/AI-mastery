# Retrieval

**Retrieval** = given a user query, find the most relevant chunks from the vector database.

## Top-k Retrieval
Embed the query, return k most similar chunks by cosine similarity.
```python
# Pseudocode
query_vec = embed(user_query)
results = vector_db.search(query_vec, k=5)
```
`k` is typically 3–10. More chunks = more context but higher cost and noise.

## Similarity Threshold
Filter out chunks below a similarity score:
```python
results = [r for r in raw_results if r.score > 0.75]
```
Prevents injecting irrelevant chunks when no good match exists.

## Metadata Filtering
Filter by document properties before or during vector search:
```python
results = vector_db.query(
    query_embedding=query_vec,
    filter={"department": "legal", "year": 2024},
    k=5
)
```
Critical for multi-tenant systems or when you have documents from different domains.

## Multi-Query Retrieval
Generate multiple query variations → retrieve for each → deduplicate:
```python
queries = llm.generate_variations(user_query, n=3)
all_results = []
for q in queries:
    all_results.extend(vector_db.search(embed(q), k=5))
results = deduplicate(all_results)
```
Helps when the user's phrasing is ambiguous.

## Retrieval Quality Metrics
- **Recall@k**: are the relevant docs in the top-k?
- **Precision@k**: how many of the top-k are actually relevant?
- **MRR (Mean Reciprocal Rank)**: how high is the first relevant result?

Always measure retrieval quality separately from generation quality — they're independent failure modes.
