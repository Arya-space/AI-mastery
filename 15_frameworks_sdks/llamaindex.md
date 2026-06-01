# LlamaIndex

LlamaIndex is a RAG-first framework. Where LangChain is general-purpose, LlamaIndex is deeply optimized for indexing, querying, and retrieving over documents.

## Core Concepts

### Documents and Nodes
```python
from llama_index.core import SimpleDirectoryReader

# Load all documents from a folder
documents = SimpleDirectoryReader("./data").load_data()
# Each Document → split into Nodes (chunks) with metadata
```

### Index Types
LlamaIndex has multiple index types for different use cases:

| Index | How it works | Best for |
|-------|-------------|----------|
| `VectorStoreIndex` | Embeds nodes, vector search | Semantic Q&A |
| `SummaryIndex` | Chains through all nodes | Summarization |
| `KeywordTableIndex` | BM25 keyword matching | Exact term search |
| `KnowledgeGraphIndex` | Builds a knowledge graph | Relationship queries |

### VectorStoreIndex (most common)
```python
from llama_index.core import VectorStoreIndex

index = VectorStoreIndex.from_documents(documents)
query_engine = index.as_query_engine()

response = query_engine.query("What is the refund policy?")
print(response.response)           # answer
print(response.source_nodes)       # source chunks used
```

### Query Engines
```python
# Simple retrieval + generation
query_engine = index.as_query_engine(similarity_top_k=5)

# With streaming
query_engine = index.as_query_engine(streaming=True)
streaming_response = query_engine.query("Summarize the document")
streaming_response.print_response_stream()
```

### Retrievers
```python
retriever = index.as_retriever(similarity_top_k=10)
nodes = retriever.retrieve("What are the payment terms?")
for node in nodes:
    print(node.score, node.text[:100])
```

### Persist and Load Index
```python
# Save
index.storage_context.persist(persist_dir="./storage")

# Load (no re-embedding)
from llama_index.core import StorageContext, load_index_from_storage
storage_context = StorageContext.from_defaults(persist_dir="./storage")
index = load_index_from_storage(storage_context)
```

### Advanced: Sub-Question Query Engine
Breaks complex questions into sub-questions, queries each, synthesizes:
```python
from llama_index.core.query_engine import SubQuestionQueryEngine
from llama_index.core.tools import QueryEngineTool

tools = [QueryEngineTool.from_defaults(query_engine, name="docs", description="Company docs")]
engine = SubQuestionQueryEngine.from_defaults(query_engine_tools=tools)
response = engine.query("Compare Q1 and Q2 revenue and explain the difference")
```

## LlamaIndex vs LangChain for RAG
| | LlamaIndex | LangChain |
|--|-----------|-----------|
| RAG depth | Deeper, more options | Good but less specialized |
| Index types | Many built-in | Mainly vector |
| Document loaders | Many | Many |
| Agent support | Good | Good |
| Learning curve | Steeper | Gentler |

## When to Use LlamaIndex
- Complex RAG with multiple document types
- Need advanced retrieval (sub-question, knowledge graph)
- Document-heavy applications (legal, financial, research)
- When you need fine-grained control over indexing
