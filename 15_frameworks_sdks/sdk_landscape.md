# SDK Landscape Software Development Kit.

The AI engineering ecosystem has many SDKs. Know which layer each belongs to.

## Layer 1: Provider SDKs (Direct Model Access)
Talk directly to model APIs. No abstractions.

| SDK | Install | Models |
|-----|---------|--------|
| `openai` | `pip install openai` | GPT-4o, o1, embeddings, images |
| `anthropic` | `pip install anthropic` | Claude 3.5/4.x |
| `google-generativeai` | `pip install google-generativeai` | Gemini 1.5/2.0 |
| `mistralai` | `pip install mistralai` | Mistral, Mixtral |
| `cohere` | `pip install cohere` | Command R+, reranking, embeddings |
| `openai` (Azure) | same SDK, different base URL | OpenAI models on Azure |

**When to use**: always use provider SDKs directly for simple tasks. Don't add a framework just to make one API call.

## Layer 2: Orchestration Frameworks
Build pipelines, RAG, and agents on top of provider SDKs.

| Framework | Focus | Best For |
|-----------|-------|----------|
| LangChain | General — chains, RAG, agents | RAG pipelines, document processing |
| LangGraph | Stateful agent graphs | Complex multi-step agents |
| LlamaIndex | RAG-first | Document indexing, query engines |
| OpenAI Agents SDK | Agents + handoffs | Agent systems (OpenAI ecosystem) |
| Anthropic Agent SDK | Claude-native agents | Claude-based agent systems |

## Layer 3: Evaluation + Observability
| Tool | Use |
|------|-----|
| LangSmith | Tracing + evals for LangChain apps |
| Langfuse | Open-source, any framework |
| RAGAS | RAG-specific eval metrics |
| Weights & Biases | Experiment tracking + traces |

## Layer 4: Vector DB SDKs
| SDK | Type |
|-----|------|
| `chromadb` | Local, prototyping |
| `pinecone` | Managed cloud |
| `qdrant-client` | Self-hosted / cloud |
| `weaviate-client` | Hybrid search |
| `pgvector` (via psycopg2) | PostgreSQL extension |

## Which to Learn First

```
1. openai + anthropic        ← must know, use daily
2. fastapi + pydantic        ← backend layer
3. LangChain OR LlamaIndex   ← RAG pipelines
4. LangGraph OR Agents SDK   ← agent patterns
5. Langfuse                  ← observability
6. chromadb → pinecone       ← vector DBs
```

**Rule**: learn raw SDKs before frameworks. Frameworks abstract things you need to understand first. If you can't build a RAG pipeline from scratch with `openai` + `numpy`, don't use LangChain yet.
