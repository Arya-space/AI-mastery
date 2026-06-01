#  Language Services Tool

## What Is This Project?

A production Azure-powered NLP application built for my internship. It processes audio recordings, video files, and documents — transcribing speech, refining transcripts, generating Minutes of Meeting, translating content, and performing sentiment analysis.

**Stack:** Python · Streamlit · Azure OpenAI · Azure Speech Services · Azure AI Language · Azure Translator · Azure Blob Storage

---

## Architecture Pattern

**Multi-service Azure AI Platform** — different Azure cognitive services handle different input types, all orchestrated through a single Streamlit UI.

```
Client Layer     → Streamlit Web App (app.py / main())
Application Layer→ app_io/ modules (readers, exporters, media)
Service Layer    → services/ (speech, language, translator, AOAI, vision OCR)
Transcript Layer → transcript/ (timecodes, speakers, minutes, witness)
Config Layer     → config/ (settings, clients, logging)
Storage Layer    → Azure Blob Storage (ephemeral container for audio jobs)
AI Layer         → Azure Speech · Azure OpenAI · Azure Translator · Azure Language
```

---

## The Two Input Paths

### Path 1 — Audio / Video
```
Upload → Normalize to WAV 16kHz mono → Upload to Azure Blob
→ Generate SAS URL → Create Batch Transcription Job (Azure Speech)
→ Poll until done → Download JSON results
→ Parse recognizedPhrases → "Speaker N [HH:MM:SS]: text" format
→ LLM speaker detection → User speaker assignment
→ Optional: LLM refinement → Analysis services
```

### Path 2 — Documents (PDF / DOCX / Image)
```
Upload → Extract text
  ├── PDF   → pdfplumber → OCR fallback if text < 100 chars
  ├── DOCX  → structured element extraction (paragraphs + tables) → OCR fallback
  └── Image → Azure Vision OCR directly
→ Detect language → Translate to English if needed
→ Classify document type via LLM → Analysis services
```

---

## Project Structure

```
app.py                    ← Entry point (Streamlit page config + main())
app_io/
  readers.py              ← PDF/DOCX text extraction + structured elements
  exports_docx.py         ← DOCX builder (transcript template, interview template)
  exports_pdf.py          ← PDF export from results dict
  media.py                ← Audio normalization (WAV 16kHz mono), date extraction
config/
  settings.py             ← Reads .env / Azure credentials
  clients.py              ← AzureOpenAI + TextAnalyticsClient singletons
  logging_setup.py        ← Logging config
services/
  speech_et.py            ← Blob upload, SAS URL, batch transcription job
  azure_language.py       ← Speaker-based sentiment analysis
  translator.py           ← Text + DOCX element translation
  aoai.py                 ← LLM calls: doc type detection, refinement, summarization
  vision_ocr.py           ← Azure Vision OCR for images, PDFs, DOCX images
transcript/
  timecodes.py            ← ticks → HH:MM:SS conversion
  text_utils.py           ← Language detection, sentiment normalization, speaker masking
  speakers.py             ← LLM speaker label extraction + normalization
  minutes.py              ← Minutes of Meeting generation (chunk → notes → merge)
  witness.py              ← Summarization by Speaker generation
python-client/            ← Auto-generated Swagger client for Azure Speech REST API
```

---

## The Four Analysis Services

| Service | Input | What it does |
|---------|-------|-------------|
| Minutes of Meeting | Transcription | Chunks transcript → LLM notes per chunk → merges into structured Markdown MoM |
| Summarization by Speaker | Transcription | Filters one speaker's turns → LLM generates a summary of their contributions |
| Sentiment Analysis | Transcription or Document | Azure Language service scores each speaker's turns: Positive / Neutral / Negative |
| Summarization | Document (non-transcript) | Routes to specialized LLM prompt based on doc type |
| Translation | Any | Azure Translator; DOCX path preserves table/paragraph structure via element-level translation |

---

## Document Type Routing

LLM classifies the document into one of: `report`, `legal document`, `proposal`, `financial document`, `policy document`, `case study`, `transcription`, `other`. Each routes to a different prompt:

```python
if doc_type == "report":           summarize_report(en_text)
elif doc_type == "legal document": summarize_legal_document(en_text)
elif doc_type == "proposal":       summarize_proposal(en_text)
# ... etc
```

Non-English documents are auto-translated to English before classification and summarization.

---

## Speaker Pipeline (Transcription Flow)

```
Raw transcript: "Speaker 1 [00:01:23]: text..."
  ↓
llm_extract_speaker_labels()       → raw_labels + suggested_names (LLM guess)
  ↓
normalize_transcript_to_speakers() → canonical "Speaker N" format
  ↓
User form: rename Speaker N → real name, pick priority speakers
  ↓
apply_speaker_name_mapping()       → named transcript for display/download
  ↓
Optional: refine_transcription_in_chunks() → LLM cleans filler words, grammar
  ↓
Analysis services use named, refined transcript
```

**Key design:** the internal transcript always stays in `Speaker N` canonical form. Names are only applied at display/download time via `apply_speaker_name_mapping()`. This means speaker reassignment never requires re-running transcription.

---

## Azure Speech Batch Transcription

The project uses Azure's **REST-based batch transcription API** (not the real-time SDK), surfaced through a Swagger-generated Python client:

```
File → ensure_wav_16k_mono()
     → upload_file_to_private_blob_et()
     → generate_sas_url_et()              ← time-limited SAS token
     → create_transcription_job_et()      ← polls until Succeeded/Failed
     → download_ephemeral_files_et()      ← downloads result JSON files locally
     → Parse recognizedPhrases[]          ← each phrase: speaker, offset, nBest[0].display
```

Timestamps use **ticks** (100-nanosecond units) converted to HH:MM:SS via `ticks_to_hhmmss()`.

---

## State Management Pattern

Streamlit reruns the entire script on every interaction. The app uses `st.session_state` as a persistent in-memory store:

| Key | Holds |
|-----|-------|
| `final_text` | Raw extracted / transcribed text |
| `refined_text` | LLM-refined transcript (Speaker N labels) |
| `refined_text_named` | Refined + real names applied |
| `speaker_data` | `{Speaker N: real_name}` mapping |
| `doc_type` | Detected document type string |
| `analysis_downloads` | Dict of generated PDF/DOCX bytes for download |
| `translation_text` | Cached translation output |

File change detection: when `uploaded_file.name != session_state["uploaded_filename"]`, all derived state is cleared and re-derived from scratch.

---

## Azure Services Used

| Service | SDK / Client | Purpose |
|---------|-------------|---------|
| Azure OpenAI | `openai.AzureOpenAI` | Refinement, summarization, doc type detection, speaker extraction |
| Azure Speech | Swagger REST client | Batch transcription with diarization (speaker separation) |
| Azure AI Language | `azure.ai.textanalytics` | Sentiment analysis per speaker |
| Azure Translator | `requests` to REST API | Text and DOCX element translation |
| Azure Vision | REST API | OCR for images, scanned PDFs, embedded DOCX images |
| Azure Blob Storage | `azure.storage.blob` | Ephemeral audio file holding for batch transcription SAS URLs |

---

## Key Engineering Decisions

| Decision | Reason |
|----------|--------|
| Batch transcription over real-time | Long recordings (interviews, meetings) — no streaming needed |
| Swagger-generated client for Speech API | Azure Speech batch API is REST-only; avoids raw `requests` calls |
| Speaker masking during translation | Prevents Azure Translator from mangling `Speaker 1:` labels |
| `@st.fragment` on download buttons | Prevents full Streamlit rerun when user clicks download |
| Two transcript versions (canonical + named) | Decouples speaker ID from display name; rename doesn't re-run LLM |
| OCR fallback for PDF/DOCX | Handles scanned documents where pdfplumber/python-docx returns < 100 chars |
| Element-level DOCX translation | Preserves table and paragraph structure instead of translating flat text |
| WAV 16kHz mono normalization | Azure Speech requires this format; `media.py` handles via ffmpeg/pydub |

---

## Output Formats

Every analysis result downloadable as:
- **DOCX** — via `python-docx`, optionally from a branded UNDP template
- **PDF** — via `fpdf`

Transcripts specifically produce:
- Raw transcript DOCX (speaker labels from Azure)
- Refined transcript DOCX (LLM-cleaned, real speaker names)
- Witness Interview Template DOCX
- Subject Interview Template DOCX

---

## Module Cross-References

| Module | What this project demonstrates |
|--------|-------------------------------|
| Module 02 — Prompt Engineering | Specialized prompts per document type; MoM chunk-then-merge pattern |
| Module 09 — Agents | Multi-step conditional flow (upload → transcribe → assign → refine → analyze) |
| Module 11 — Memory | Full session state persistence across Streamlit reruns |
| Module 13 — Safety | Prompts forbid inventing names/details; only use source transcript |
| Module 14 — Production | Azure cloud stack + packaged as `.exe` via PyInstaller |
