# Agent vs Workflow

## Workflow
A **workflow** is a fixed, deterministic sequence of steps defined by the developer.

```python
# Workflow: always runs these steps in this order
def process_document(doc):
    text = extract_text(doc)
    summary = llm_summarize(text)
    entities = llm_extract_entities(text)
    store_to_db(summary, entities)
    return {"summary": summary, "entities": entities}
```
- Control flow: developer decides
- Predictable, testable, auditable
- No autonomy — model just executes each step

## Agent
An **agent** lets the model decide the sequence of actions.

```
Goal: "Process this document and extract relevant financial data"
→ Model decides: read doc → summarize → extract numbers → validate → store
→ If errors: model decides to re-try or ask for clarification
```
- Control flow: model decides
- Flexible, can handle unexpected situations
- Less predictable, harder to audit

## When to Use Each

| Situation | Use |
|-----------|-----|
| Steps are always the same | Workflow |
| Steps vary based on the task | Agent |
| You need auditability | Workflow |
| Task requires judgment calls | Agent |
| High reliability required | Workflow |
| Task is open-ended | Agent |
| Simple, well-defined pipeline | Workflow |

## Hybrid: Agents Inside Workflows
Most production systems use both:
```
Workflow: validate input → run agent → validate output → store result
```
The workflow provides structure and safety.
The agent handles the flexible/complex reasoning part.

## Key Insight
More autonomy = more capability but less reliability.
Match the autonomy level to the task's complexity and risk.
