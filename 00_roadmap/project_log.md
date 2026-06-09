# Project: AI Study Assistant

A chatbot that answers questions about these study notes — built incrementally across every section of this course.

By the end it will be embedded in this website, powered by Gemini (GCP), with RAG over your notes using Pinecone, and exposed as an MCP server.

---

## The Stack

| Layer | Tool | When introduced |
|-------|------|----------------|
| LLM | Ollama (local) → Gemini (production) | Section 02 |
| Backend | FastAPI | Section 15 |
| Memory | In-memory → Firestore | Section 03 |
| Embeddings | Vertex AI / sentence-transformers | Section 06 |
| Vector DB | Pinecone | Section 07 |
| Tool calling | Custom tools | Section 08 |
| MCP | MCP server over your notes | Section 09 |
| Agents | Multi-step reasoning | Section 10 |
| Deployment | Cloud Run (GCP) | Section 15 |
| Frontend | Chat widget on GitHub Pages | Section 15 |

---

## How to Run Locally

```bash
# 1. Install Ollama
brew install ollama
ollama pull llama3.2

# 2. Clone repo and install dependencies
pip install -r requirements.txt

# 3. Copy env template and fill in your keys
cp .env.example .env

# 4. Run a notebook
jupyter notebook
```

---

## Environment Variables

Copy `.env.example` to `.env` and fill in:

```
# Local (Ollama — no key needed)
OLLAMA_BASE_URL=http://localhost:11434/v1

# GCP / Gemini (for production)
GCP_PROJECT_ID=your-project-id
GEMINI_API_KEY=your-key

# Pinecone (for RAG — Section 07+)
PINECONE_API_KEY=your-key
PINECONE_INDEX=ai-study-assistant
```

---

## Build Log

### Notebook 01 — First API Call (`02_llm_apis/`)
**Status:** In progress

What it builds:
- Connect to Ollama locally
- Make a single-turn API call
- Explore the raw response object
- Compare OpenAI-compatible vs native SDK syntax
- **Project:** Ask the assistant one question about your notes

---

### Notebook 02 — Memory + Multi-turn (`03_memory_context/`)
**Status:** Not started

What it builds:
- Add conversation history
- Implement context window trimming
- **Project:** Assistant remembers the conversation across turns

---

### Notebook 03 — Prompt Engineering (`04_prompt_engineering/`)
**Status:** Not started

What it builds:
- Write a proper system prompt for the assistant
- Test different prompting strategies
- **Project:** Assistant has a persona and follows rules

---

### Notebook 04 — Structured Output (`05_structured_outputs/`)
**Status:** Not started

What it builds:
- Assistant returns structured JSON: answer + confidence + source section
- **Project:** Responses are parseable and reliable

---

### Notebook 05 — Embeddings (`06_embeddings_search/`)
**Status:** Not started

What it builds:
- Embed all study notes
- Find similar sections by query
- **Project:** Assistant can find relevant notes before answering

---

### Notebook 06 — RAG (`07_rag/`)
**Status:** Not started

What it builds:
- Load notes into Pinecone
- Retrieve relevant chunks before every LLM call
- **Project:** Assistant answers from your actual notes, with citations

---

### Notebook 07 — Tool Calling (`08_tool_calling/`)
**Status:** Not started

What it builds:
- Add tools: `search_notes`, `get_section`, `define_term`
- **Project:** Assistant actively searches instead of passively retrieving

---

### Notebook 08 — MCP Server (`09_mcp/`)
**Status:** Not started

What it builds:
- Wrap the assistant as an MCP server
- Expose notes as MCP resources
- **Project:** Assistant works in Claude Desktop + your website

---

### Notebook 09 — Agents (`10_agents/`)
**Status:** Not started

What it builds:
- Multi-step reasoning: plan → search → answer → verify
- **Project:** Assistant handles complex multi-part questions

---

### Notebook 10 — Deploy (`15_production/`)
**Status:** Not started

What it builds:
- FastAPI backend on Cloud Run
- Chat widget on GitHub Pages
- **Project:** Live on your website
