# OAI Intake & Assessment Tool

## What Is This Project?

A production RAG application built for UNDP's **Office of Audit and Investigations (OAI)**. It uses AI to screen and assess misconduct complaints, replacing manual document review with structured LLM-generated reports.

**Stack:** Python · Streamlit · Azure OpenAI · Azure AI Search · Azure Blob Storage

---

## Architecture Pattern

**Enterprise RAG with Azure Cognitive Stack** — a layered N-tier architecture where the AI processing layer is decoupled from the application layer, connected through a managed document ingestion pipeline and a vector search index.

```
Client Layer       → Streamlit Web App
Application Layer  → FastAPI Backend / core/ modules
Data Storage Layer → Azure Blob Storage (raw + processed containers)
AI Processing Layer→ Azure OpenAI + Azure AI Search (Skillset Pipeline)
Security Layer     → Azure Key Vault (cross-cutting)
```

The **Skillset Pipeline** inside Azure AI Search is the RAG indexing engine:
```
Documents → OCR → Text Extraction → Chunking → Embedding → Search Index
```

---

## SDKs Used

```
openai>=1.30.0                  ← AzureOpenAI class (chat + embeddings)
azure-storage-blob>=12.19.0     ← BlobServiceClient
azure-search-documents>=11.5.1  ← SearchClient, SearchIndexClient
azure-identity>=1.15.0          ← AzureKeyCredential
```

> Key point: OpenAI comes from the **OpenAI Python SDK**, not the Azure SDK family. You just point it at an Azure endpoint.

---

## Project Structure

```
Home.py                   ← Landing page
pages/
  1_Chatbot.py            ← RAG chatbot (3 indexes)
  2_Intake.py             ← Intake & Triage workflow
  3_Assessment.py         ← Case Assessment workflow
core/
  config.py               ← reads .env / Azure credentials
  llm.py                  ← AzureOpenAI wrapper + tenacity retry
  blob.py                 ← all blob storage operations
  search.py               ← embed, index, retrieve
  extractor.py            ← text extraction (.msg/.pdf/.eml/.txt)
  case.py                 ← case ID helpers (TMP-xxx, IS-YYYY-NNNNN)
  chat_rag.py             ← RAG pipeline for chatbot
prompts/
  system_role.txt         ← AI persona + strict reporter/complainant rules
  long_report.txt         ← detailed screening report instructions
  short_report.txt        ← triage summary instructions
  assessment_report.txt   ← full case assessment instructions
  chatbot_prompt.txt      ← chatbot system prompt
```

---

## The Three Tools

### 1. Intake & Triage (SOP 620)
Screens new complaints. Three flows:

| Flow | What it does |
|------|-------------|
| New Complaint | Upload → screen → get reports → register EP number → Retain or Close |
| Triage: Additional Files | Add files to existing case → re-run screening |
| Triage: Close | Close case + delete all files permanently |

**New complaint lifecycle:**
```
Generate TMP-id → Upload files → Extract text → Index in Azure Search
→ Run LLM screening → Short Report + Long Report
→ User registers in EthicsPoint → Enter IS-YYYY-NNNNN
→ Decision: Retain Case OR Close: No Further Action
→ Move blobs TMP → EP container, update search index metadata
```

**Two LLM reports generated (JSON):**
- **Short report** — complainant, subject, location, category, anonymized summary
- **Long report** — file summaries, type of wrongdoing, jurisdiction, priority (High/Low/Cannot classify)

### 2. Assessment (SOP 621)
Deeper structured analysis of an existing case.

Assessment report includes:
- Case summary + key allegations
- Evidence quality (Strong / Moderate / Weak / Insufficient)
- Risk Assessment Checklist (RAC) — high and low priority factors
- **Recommended outcome:** Investigation / Expedited Investigation / Closure / Referral

### 3. Chatbot
RAG Q&A with step-based routing:
```
Greeting → Warning → Subject Picker
  ├── SOPs              → queries sop index
  ├── Rules & Regs      → queries rules-regs-index (with doc filter)
  └── Specific Case     → enter EP number → queries intake-assessment-index
```

---

## The RAG Pipeline

```python
# For screening/assessment:
text = extract_text(file_bytes, filename)   # extractor.py
vector = embed(text[:8000])                 # text-embedding-3-large, 3072 dims
sop_chunks = azure_search(query, vector)    # hybrid + semantic search
prompt = system_role + report_instructions + case_text + sop_context
response = AzureOpenAI.chat(prompt)         # JSON output
```

**Token management:**
- 80K chars per file cap
- 120K chars total combined cap (screening)
- 60K chars total cap (assessment)
- 1500 chars per SOP chunk, max 6 chunks retrieved

---

## Data Storage Layout

```
temp container (before EP number assigned):
  cases/TMP-xxxxxxxx/
    raw/      ← uploaded files
    reports/  ← long_report.json, short_report.json
    events/   ← pending_deletion.json (auto-purge after 30 days)

intake-assessment-documents (permanent):
  cases/IS-YYYY-NNNNN/
    raw/      ← uploaded files
    reports/  ← reports
    events/   ← closed.json (audit log)
```

---

## Azure AI Search — Two Indexes

| Index | Content | Used for |
|-------|---------|----------|
| `sop` | SOP / policy reference docs | RAG context during screening |
| `intake-assessment-index` | Case documents (chunked + embedded) | Case lookup, chatbot case Q&A |

**Index schema (intake):**
```
chunk_id    → unique key
parent_id   → TMP-xxx (before EP) or IS-YYYY-NNNNN (after)
chunk       → extracted text
title       → filename
text_vector → 3072-dim embedding
ep_number   → IS-YYYY-NNNNN (filterable, set after assignment)
```

---

## Why Raw SDKs Instead of LangChain

| Raw SDK Approach (this project) | LangChain |
|--------------------------------|-----------|
| Full control over every step | Abstracts steps away |
| Easy to debug — print what LLM sees | Needs LangSmith tracing |
| Custom token caps, JSON cleaning, retry | Fighting the framework for custom behaviour |
| Stable API surface | Frequent breaking changes |
| One fewer dependency layer | Adds LangChain on top |

> **Rule of thumb:** LangChain for prototyping. Raw SDKs for production.

---

## This Project as a Hand-Rolled LangGraph

The Intake flow uses `st.session_state["step"]` and `if/elif step ==` blocks — this is exactly what LangGraph's `StateGraph` nodes and edges do formally. The project is a manually implemented state machine agent.

```
step = "upload" → step = "extract" → step = "screen" → step = "register" → step = "decide"
```

---

## Key Engineering Decisions

| Decision | Reason |
|----------|--------|
| Two-container blob pattern | Keeps unverified complaints separate until EP number confirmed |
| TMP → EP number transition | Preserves blob files + search vectors, just updates metadata |
| `tenacity` retry (5 attempts, exponential backoff) | Azure OpenAI 500/rate-limit errors are transient |
| Separate embedding deployments for SOP vs intake | Both use `text-embedding-3-large` (3072 dims) but different endpoints |
| Prompts as `.txt` files | Easy to update without touching code |
| `st.cache_resource` for LLM + blob clients | Avoid re-initialising heavy clients on every Streamlit rerun |

---

## Module Cross-References

| Module | What this project demonstrates |
|--------|-------------------------------|
| Module 05 — Embeddings + Search | SOP retrieval: embed → vector search → inject context |
| Module 06 — RAG | Full RAG pipeline with hybrid + semantic search |
| Module 09 — Agents | Multi-step state machine (hand-rolled LangGraph) |
| Module 11 — Memory | Chat history in `st.session_state`, case context from search index |
| Module 13 — Safety | System prompt strict prohibitions, no inference beyond source text |
| Module 14 — Production | Azure cloud stack, packaged as `.exe` via PyInstaller |
