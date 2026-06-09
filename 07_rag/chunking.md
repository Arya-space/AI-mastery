# Chunking

**Chunking** = splitting documents into smaller passages for embedding and retrieval. Chunk quality directly impacts RAG quality.

## Why Chunking Matters
- Embedding models have a token limit (usually 512–8192 tokens)
- Smaller chunks → more precise retrieval
- Too small → loses context; too large → retrieves irrelevant content

## Strategies

### Fixed-Size with Overlap
```python
from langchain.text_splitter import RecursiveCharacterTextSplitter

splitter = RecursiveCharacterTextSplitter(
    chunk_size=512,       # characters (not tokens)
    chunk_overlap=50,     # overlap to avoid cutting context mid-sentence
    separators=["\n\n", "\n", ".", " "]
)
chunks = splitter.split_documents(docs)
```
Overlap prevents information loss at chunk boundaries.

### Sentence-Based
Split at sentence boundaries. Preserves grammatical units.
Better than fixed-size for prose documents.

### Semantic Chunking
Group sentences by semantic similarity — new chunk when topic changes.
More accurate, slower to compute.
```python
from langchain_experimental.text_splitter import SemanticChunker
splitter = SemanticChunker(embedding_model)
```

## Chunk Size Tradeoffs
| Small chunks (~200 tokens) | Large chunks (~1000 tokens) |
|---------------------------|---------------------------|
| Precise retrieval | More context per chunk |
| May lose context | May retrieve irrelevant content |
| More chunks = higher vector DB storage | Fewer chunks, smaller index |

## Parent-Child Pattern
Store **child chunks** (small, for retrieval) but return **parent chunks** (large, for context) to the LLM.
```
Parent: full paragraph (500 tokens)
Children: 3 × 150 token sub-passages

Retrieve: child chunk by similarity
Return to LLM: parent chunk (full context)
```
Best of both worlds: precise retrieval + full context.

## Rule of Thumb
Start with `chunk_size=512, overlap=50`. Measure retrieval quality. Adjust based on your document type.
