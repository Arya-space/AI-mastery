# Vector Databases

A **vector database** stores embeddings and enables fast nearest-neighbor search at scale.

## Why a Vector Database?
- Brute-force similarity search = O(n) — too slow for millions of vectors
- Vector DBs use **ANN (Approximate Nearest Neighbor)** algorithms for O(log n) search
- Trade tiny accuracy loss for massive speed gain

## ANN Algorithms
- **HNSW** (Hierarchical Navigable Small World): graph-based, fast, most commonly used
- **IVF** (Inverted File Index): clusters vectors, searches only relevant clusters
- **LSH** (Locality-Sensitive Hashing): hash similar vectors to same bucket

## Major Options

| DB | Type | Best For |
|----|------|----------|
| FAISS | Local library | Fast experiments, no persistence |
| Chroma | Local server | Prototyping, development |
| pgvector | PostgreSQL ext | Production, already using Postgres |
| Pinecone | Managed cloud | Scale, fully managed |
| Weaviate | Managed/self-hosted | Hybrid search built-in |
| Qdrant | Self-hosted/cloud | High performance, filtering |

## FAISS (Local)
```python
import faiss
import numpy as np

dim = 1536
index = faiss.IndexFlatIP(dim)  # Inner product (dot product)

# Add vectors
vectors = np.array([...], dtype=np.float32)
index.add(vectors)

# Search
query = np.array([[...]], dtype=np.float32)
scores, indices = index.search(query, k=5)  # top 5
```

## Chroma (Prototyping)
```python
import chromadb

client = chromadb.Client()
collection = client.create_collection("docs")

collection.add(
    embeddings=[[0.1, 0.2, ...]],
    documents=["doc text"],
    ids=["doc1"]
)

results = collection.query(query_embeddings=[[0.1, 0.2, ...]], n_results=5)
```

## Key Concepts
- **Metadata filtering**: filter by document type, date, source before vector search
- **Namespaces / collections**: separate vector spaces for different data
- **Upsert**: add or update vectors by ID
