# Document Loading

First step of RAG indexing: extract clean text + metadata from source documents.

## Common Formats
| Format | Tool |
|--------|------|
| PDF | `pypdf`, `pdfplumber`, `unstructured` |
| HTML | `BeautifulSoup`, LangChain `WebBaseLoader` |
| DOCX | `python-docx`, `unstructured` |
| CSV | `pandas`, LangChain `CSVLoader` |
| Markdown | Direct string read |
| Database | SQL query → text |

## LangChain Loaders
```python
from langchain_community.document_loaders import PyPDFLoader, WebBaseLoader

# PDF
loader = PyPDFLoader("document.pdf")
docs = loader.load()  # List of Document objects

# Web
loader = WebBaseLoader("https://example.com/docs")
docs = loader.load()

# Each Document has:
# doc.page_content — the text
# doc.metadata — {"source": "...", "page": 1, ...}
```

## Metadata Is Important
Always preserve and enrich metadata during loading:
```python
for doc in docs:
    doc.metadata["source"] = "annual_report_2024"
    doc.metadata["department"] = "finance"
```
Metadata enables **filtered retrieval** later (e.g., "only search Q3 docs").

## Handling Edge Cases
- **Tables in PDFs**: often extracted as messy text; use `pdfplumber` for table extraction
- **Images**: require OCR (e.g., `pytesseract`) or multimodal models
- **Encoding issues**: always open files with `encoding="utf-8"`, handle `errors="replace"`
- **HTML noise**: strip nav, footers, ads before chunking

## Unstructured Library
Handles multiple formats with one API:
```python
from unstructured.partition.auto import partition
elements = partition("document.pdf")
text = "\n".join([e.text for e in elements])
```
Good for mixed-format document pipelines.
