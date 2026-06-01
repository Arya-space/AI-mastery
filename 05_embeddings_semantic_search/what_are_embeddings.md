# What Are Embeddings

An **embedding** is a fixed-length vector of floats that encodes the semantic meaning of text. Similar meaning → mathematically close vectors.

## Shape and Values
- Vector: `[0.012, -0.432, 0.981, ...]`
- Dimensions: 384 (small) → 1536 (OpenAI small) → 3072 (OpenAI large)
- Values: floats between roughly -1 and 1
- One vector per input (sentence, paragraph, document)

## How Meaning Is Encoded
The model is trained so that:
- "dog" and "puppy" → close vectors
- "Paris" and "France" → close vectors
- "bank" (river) and "bank" (finance) → different vectors depending on context

No human defines the axes. The model learns them from data.

## Embedding Models vs Generative LLMs
- Embedding models: **encoder-only** transformers, output a vector, fast + cheap
- Generative LLMs: **decoder-only** transformers, output text, slow + expensive

## Choosing a Model
| Model | Dims | Use Case |
|-------|------|----------|
| `text-embedding-3-small` | 1536 | OpenAI, good default |
| `text-embedding-3-large` | 3072 | OpenAI, higher accuracy |
| `all-MiniLM-L6-v2` | 384 | Local, fast, free |
| `bge-large-en-v1.5` | 1024 | Local, state-of-the-art open source |

## Sentence vs Word Embeddings
- **Word embeddings** (Word2Vec, GloVe): one vector per word, static, no context
- **Sentence embeddings** (BERT, Sentence-BERT): one vector per sentence, context-aware
- Always use sentence embeddings for semantic search and RAG
