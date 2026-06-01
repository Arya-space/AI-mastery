# RAG vs Fine-tuning

## What Each Changes
- **RAG**: changes what information the model has access to at inference time
- **Fine-tuning**: changes the model's weights — its behavior, style, format, tone

A common mistake: "The model doesn't know our domain → fine-tune it."
Better answer: first try RAG or prompting. Fine-tuning for knowledge injection rarely works as well as RAG.

## Fine-tuning Does NOT Reliably
- Teach the model new facts (it forgets and hallucinates)
- Replace RAG for private knowledge access
- Guarantee the model uses the new knowledge consistently

## Fine-tuning DOES
- Teach consistent output format / style
- Reduce system prompt length (bake instructions into weights)
- Improve domain-specific reasoning patterns
- Teach the model to follow a specific response structure reliably

## RAG vs Fine-tuning Comparison
| | RAG | Fine-tuning |
|--|-----|-------------|
| Knowledge injection | Yes (reliable) | No (unreliable) |
| Format/style control | Partially (prompt) | Yes |
| Data updates | Instant (re-index) | Requires retraining |
| Citeable sources | Yes | No |
| Cost | Vector DB + API | Training + serving |
| Effort | Moderate | High |

## Combining Both
Best of both worlds:
- Fine-tune for format, style, domain language
- RAG for up-to-date knowledge injection
- Used by enterprise systems with high quality requirements

## Interview Answer
"Fine-tuning changes the model's behavior. RAG changes what information it has access to. For knowledge, use RAG. For behavior, use fine-tuning."
