# Why Structured Outputs

## The Problem with Free-Text Responses
LLMs return natural language by default. Software needs predictable, parseable data.

```python
# Free text — you can't reliably use this in code
"The sentiment is positive with high confidence."

# Structured — you can
{"sentiment": "positive", "confidence": 0.92}
```

## Why Structure Matters in Production
- Parse without regex hacks
- Validate before downstream use
- Type-safe in your application
- Consistent across thousands of calls
- Testable: you know exactly what to check

## Use Cases
- **Extraction**: pull entities, dates, amounts from documents
- **Classification**: return a label + confidence score
- **Data transformation**: convert unstructured text to DB records
- **Decision making**: model returns an action + reasoning
- **Tool calling**: model returns which tool to call + arguments

## Tradeoffs
| Structured | Free-text |
|-----------|-----------|
| Reliable parsing | Flexible, natural |
| Requires schema design | No upfront schema work |
| Slightly reduces creativity | Better for open-ended tasks |
| Easier to validate + test | Harder to validate |

## Rule of Thumb
If the output feeds into code → use structured output.
If the output goes directly to a human → free-text is fine.
