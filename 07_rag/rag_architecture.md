# RAG Architecture

## Two Pipelines

### Offline (Indexing) Pipeline — runs once / on update
```
Documents (PDF, HTML, DOCX, DB...)
    ↓
Document Parser (extract text)
    ↓
Chunker (split into passages)
    ↓
Embedding Model (text → vector)
    ↓
Vector Database (store vectors + metadata + text)
```

### Online (Query) Pipeline — runs per user request
```
User Query
    ↓
Embed query (same model as indexing)
    ↓
Vector DB search (top-k chunks)
    ↓ [optional]
Reranker (cross-encoder, pick best k)
    ↓
Build prompt (system + context chunks + query)
    ↓
LLM generates answer
    ↓
[optional] Extract citations
    ↓
Return response
```

## Naive RAG vs Advanced RAG
| | Naive RAG | Advanced RAG |
|-|-----------|-------------|
| Retrieval | Simple top-k semantic | Hybrid + reranking |
| Query | As-is | Query rewriting / expansion |
| Chunking | Fixed size | Semantic / parent-child |
| Context | Raw chunks | Compressed / summarized |

## Component Choices
| Layer | Options |
|-------|---------|
| Document loading | LangChain loaders, pypdf, unstructured |
| Chunking | LangChain splitters, LlamaIndex |
| Embedding | OpenAI, HuggingFace, Cohere |
| Vector DB | FAISS, Chroma, pgvector, Pinecone |
| Reranker | Cohere Rerank, BGE, cross-encoder |
| LLM | OpenAI, Anthropic, Llama |
| Orchestration | LangChain, LlamaIndex, custom |
