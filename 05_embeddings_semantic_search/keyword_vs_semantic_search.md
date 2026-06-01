# Keyword vs Semantic Search

## Keyword Search (BM25 / TF-IDF)
Ranks documents by **exact term frequency** and rarity.
- BM25 is the standard algorithm (used by Elasticsearch, Solr)
- Scores: how often does the query term appear? How rare is it in the corpus?
- No understanding of meaning — just string matching

**Strengths:**
- Exact matches (product codes, names, IDs)
- Rare or technical terms that embeddings might not handle well
- Fast, no GPU needed
- No embedding model required

**Weaknesses:**
- "car" won't match "vehicle"
- Synonym mismatch — fails on paraphrase
- No concept of meaning or intent

## Semantic Search (Dense Retrieval)
Converts query and documents to embeddings, finds closest vectors.

**Strengths:**
- Handles synonyms, paraphrases, related concepts
- Understands intent ("cheap flights" → budget airlines)
- Works across languages if using multilingual model

**Weaknesses:**
- Can miss exact matches if not in training distribution
- Embedding model quality varies
- Requires embedding infrastructure (GPU or API)
- More expensive to run

## When Each Wins
| Scenario | Winner |
|----------|--------|
| "Find invoice #INV-2024-001" | Keyword |
| "What's our refund policy?" | Semantic |
| Searching for a person's name | Keyword |
| "How do I cancel my subscription?" | Semantic |
| Code search for exact function name | Keyword |
| Q&A over documentation | Semantic |

## Rule of Thumb
Use **hybrid search** (both combined) for production RAG — gets the best of both worlds.
