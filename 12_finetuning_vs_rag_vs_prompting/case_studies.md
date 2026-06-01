# Case Studies

## Case 1: Customer Support Bot

**Problem**: Handle common support questions for a SaaS product.

**Attempt 1 — Prompting only**: Works for generic questions. Fails on product-specific pricing, features, and policies.

**Solution — RAG**: Index product docs, pricing pages, FAQ. Retrieve relevant chunks per question.

**Result**: Accurate, citable answers. Update docs → bot automatically knows.

**Lesson**: Product-specific knowledge → RAG, not fine-tuning.

---

## Case 2: Legal Document Summarizer

**Problem**: Law firm wants consistent 3-part summaries (key parties, obligations, risks) from contracts.

**Attempt 1 — Prompting**: Works ~70% of the time. Occasionally misses a section or changes format.

**Solution — Fine-tuning**: 500 expert-written summaries in exact 3-part format used as training data.

**Result**: Format consistency jumps to 99%. Output style matches firm's standards.

**Lesson**: Consistent format + domain style → fine-tuning.

---

## Case 3: Code Assistant

**Problem**: Developer tool needs to help with internal codebase (custom frameworks, conventions).

**Attempt 1 — Prompting**: Model doesn't know internal APIs, generates generic code.

**Attempt 2 — Fine-tuning on codebase**: Expensive, data is too large, code changes daily.

**Solution — RAG over codebase**: Index codebase with code-aware chunking (function-level). Retrieve relevant files/functions per query.

**Result**: Accurate completions that reference actual internal APIs.

**Lesson**: Large, changing codebase → RAG. Fine-tuning for code style only.

---

## Case 4: Medical Coding Assistant

**Problem**: Classify clinical notes into ICD-10 codes consistently and accurately.

**Solution — Fine-tuning + RAG**:
- Fine-tune: model learns ICD-10 classification patterns from 10k labeled examples
- RAG: model retrieves ICD-10 code descriptions for candidate codes

**Result**: Higher accuracy than prompting alone. Fewer hallucinated codes.

**Lesson**: High accuracy on specialized classification → fine-tune + RAG together.
