# Source List

Curated references for AI Engineering study. Organized by category. Links are to official documentation or stable resources only.

---

## Core AI / LLM Sources

### Anthropic
- **Claude Prompt Engineering Guide** — https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/overview
  - System prompts, role/task/context/format, few-shot examples, chain-of-thought
- **Claude API Reference** — https://docs.anthropic.com/en/api/getting-started
  - Messages API, tool use, structured outputs, streaming
- **Claude Model Context Protocol (MCP)** — https://docs.anthropic.com/en/docs/agents-and-tools/mcp
  - MCP architecture, building servers, client integration

### OpenAI
- **OpenAI Prompt Engineering Guide** — https://platform.openai.com/docs/guides/prompt-engineering
  - Strategy overview, few-shot, chain-of-thought
- **OpenAI API Reference** — https://platform.openai.com/docs/api-reference
  - Chat completions, function calling, structured outputs, embeddings
- **OpenAI Structured Outputs** — https://platform.openai.com/docs/guides/structured-outputs
  - JSON mode, function schemas, Pydantic integration
- **OpenAI Function Calling** — https://platform.openai.com/docs/guides/function-calling
  - Tool schemas, tool choice, multi-turn tool use
- **OpenAI Agents SDK** — https://openai.github.io/openai-agents-python/
  - Agent loop, handoffs, guardrails, tracing
- **OpenAI Evals** — https://github.com/openai/evals
  - Eval framework, writing evals, running evals

### Hugging Face
- **HF LLM Course** — https://huggingface.co/learn/llm-course/en/chapter1/1
  - Transformers, tokenization, embeddings, fine-tuning
- **HF Agents Course** — https://huggingface.co/learn/agents-course/en/unit0/introduction
  - Agent architectures, tool use, multi-agent systems
- **HF Tokenizers** — https://huggingface.co/docs/tokenizers/index
  - BPE, WordPiece, tokenization details

---

## RAG and Retrieval Sources

### LangChain
- **LangChain RAG Docs** — https://python.langchain.com/docs/tutorials/rag/
  - Document loaders, splitters, retrievers, chains
- **LangChain Expression Language (LCEL)** — https://python.langchain.com/docs/concepts/lcel/
  - Composing chains and pipelines

### LlamaIndex
- **LlamaIndex Docs** — https://docs.llamaindex.ai/en/stable/
  - Alternative RAG framework, query engines, agents

### MCP
- **Model Context Protocol Specification** — https://modelcontextprotocol.io/introduction
  - Official MCP spec, architecture, tools/resources/prompts
- **MCP Python SDK** — https://github.com/modelcontextprotocol/python-sdk
  - Building MCP servers in Python

---

## Backend Engineering Sources

### FastAPI
- **FastAPI Documentation** — https://fastapi.tiangolo.com/
  - Path operations, dependency injection, async, OpenAPI
- **FastAPI Tutorial** — https://fastapi.tiangolo.com/tutorial/
  - Start here if new to FastAPI

### Pydantic
- **Pydantic v2 Docs** — https://docs.pydantic.dev/latest/
  - Data validation, models, field types, validators

### Python
- **Python typing module** — https://docs.python.org/3/library/typing.html
  - Type hints used throughout AI engineering code
- **Python asyncio** — https://docs.python.org/3/library/asyncio.html
  - Async patterns used in LLM streaming and concurrent tool calls

### Testing
- **pytest Documentation** — https://docs.pytest.org/en/stable/
  - Writing and running tests, fixtures, parametrize

---

## Data and Vector Database Sources

### FAISS
- **FAISS Documentation** — https://faiss.ai/
  - Facebook AI Similarity Search — local vector search

### Chroma
- **Chroma Documentation** — https://docs.trychroma.com/
  - Lightweight embedded vector database, good for prototyping

### pgvector
- **pgvector GitHub** — https://github.com/pgvector/pgvector
  - Vector search extension for PostgreSQL — production-friendly

### Pinecone
- **Pinecone Documentation** — https://docs.pinecone.io/
  - Managed vector database, serverless option

### Weaviate
- **Weaviate Documentation** — https://weaviate.io/developers/weaviate
  - Open-source vector database with hybrid search

---

## Production + Deployment Sources

### Docker
- **Docker Documentation** — https://docs.docker.com/
  - Containers, Dockerfiles, Compose, volumes
- **Docker Get Started** — https://docs.docker.com/get-started/
  - Start here if new to Docker

### GitHub Actions
- **GitHub Actions Documentation** — https://docs.github.com/en/actions
  - CI/CD pipelines, workflow syntax, secrets

### Cloud Providers
- **AWS documentation** — https://docs.aws.amazon.com/
- **Google Cloud documentation** — https://cloud.google.com/docs
- **Azure documentation** — https://learn.microsoft.com/en-us/azure/

---

## Optional Theory Sources

These are useful when you need to understand mechanics more deeply. Not required to start building.

| Source | What It Covers | When to Read It |
|--------|---------------|-----------------|
| Google ML Crash Course — https://developers.google.com/machine-learning/crash-course | ML fundamentals, supervised learning, neural nets | Before or during Module 01 |
| Stanford CS229 Notes — https://cs229.stanford.edu/notes2022fall/ | Linear algebra, probability, ML theory | Only if you want rigorous foundations |
| Dive into Deep Learning — https://d2l.ai/ | Neural nets, transformers, hands-on | During or after Module 01 |
| The Illustrated Transformer — https://jalammar.github.io/illustrated-transformer/ | Attention mechanism, transformer architecture | During Module 01 |
| Lilian Weng Blog — https://lilianweng.github.io/ | Agents, RAG, RLHF, memory, LLM alignment | Throughout — high-quality deep dives |
| Andrej Karpathy — Neural Networks Zero to Hero — https://karpathy.ai/zero-to-hero.html | Build transformers from scratch | Optional deep theory |

---

## Papers (Reference, Not Required Reading)

| Paper | Topic | Why It Matters |
|-------|-------|---------------|
| Attention Is All You Need (2017) | Transformer architecture | Foundational — all LLMs are based on this |
| BERT (2018) | Bidirectional transformers | Understanding encoder models and embeddings |
| GPT-3 (2020) | Large language models | Established the LLM paradigm |
| RAG (Lewis et al., 2020) | Retrieval-augmented generation | Original RAG paper |
| ReAct (2022) | Agents: reasoning + acting | Foundation for agent loop design |
| Toolformer (2023) | LLMs learning to use tools | Tool calling foundations |
| Chain-of-Thought (Wei et al., 2022) | Prompting | Why chain-of-thought prompting works |

---

## How to Use This List

1. **Start with the official API docs** for whatever model you are working with (OpenAI or Claude).
2. **Use LangChain/LlamaIndex docs** when building RAG — don't implement everything from scratch until you understand the pieces.
3. **Read Lilian Weng posts** when you need depth on agents, memory, or evaluation.
4. **Check vector DB docs** when choosing a storage backend.
5. **Use the optional theory sources** selectively — you do not need to read all of CS229 to build production AI systems.

---

*Updated: see index.md for the full module list.*
