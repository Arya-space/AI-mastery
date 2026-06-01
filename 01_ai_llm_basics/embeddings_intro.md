# Embeddings — Introduction

An **embedding** is a dense vector of numbers that represents the semantic meaning of text (or image, audio, etc.). Similar meanings → numerically close vectors.

## Embedding vs Generative Model
| | Generative LLM | Embedding Model |
|--|----------------|-----------------|
| Goal | Generate next token | Encode meaning into vector |
| Output | Text | Array of floats (e.g., 1536 dims) |
| Speed | Slow (autoregressive) | Fast (one-pass encoder) |

## Dimensions
- Each dimension = a learned axis of meaning (latent, not human-defined)
- Small models: 384–768 dims. Large models: 1536–3072 dims.
- More dims = more nuance, but more storage + slower search

## Where Embeddings Are Used
- **Semantic search**: find docs by meaning, not exact words
- **RAG**: retrieve relevant chunks before passing to LLM
- **Clustering**: group similar items automatically
- **Classification**: is this text spam? positive? relevant?

## Getting Embeddings
```python
# OpenAI
from openai import OpenAI
client = OpenAI()
response = client.embeddings.create(model="text-embedding-3-small", input="hello world")
vec = response.data[0].embedding  # list of 1536 floats

# Local (free)
from sentence_transformers import SentenceTransformer
model = SentenceTransformer('all-MiniLM-L6-v2')
vec = model.encode("hello world")  # 384 dims
```

**Key insight**: embeddings are the bridge between raw text and mathematical operations like search, clustering, and retrieval.
