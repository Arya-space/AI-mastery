# Prompting vs RAG

## When Prompting Alone Is Sufficient
- Task uses only knowledge the model already has (general facts, reasoning, coding)
- Data fits in context window
- Knowledge doesn't change frequently
- Low-stakes or creativity-focused tasks

Examples: writing code, summarizing a document you paste in, answering general questions.

## When You Need RAG
- You have private/proprietary data the model wasn't trained on
- Knowledge changes frequently (news, policies, product updates)
- Data is too large for context window
- You need verifiable citations

Examples: Q&A over company docs, customer support with product manuals, legal document search.

## Knowledge Currency
| | Prompting | RAG |
|--|-----------|-----|
| Model knowledge cutoff | Fixed | Real-time (update your docs) |
| Private data | No | Yes |
| New information | No | Yes |

## Context Window Limit
If your document is 50k tokens and your model has a 128k context: prompting works.
If your corpus is 10 million tokens: RAG is required.

## Cost Comparison
- **Prompting with full doc**: large input = expensive per call
- **RAG**: embed once + store (index cost), retrieve small chunks = cheap per call
- At scale, RAG is usually cheaper

## Rule of Thumb
Try prompting first. Add RAG when:
1. Model doesn't have the knowledge, or
2. Data is too large for context, or
3. You need updatable/private knowledge
