# Grounded Generation

**Grounded generation** = the LLM answers only using the retrieved context, not its parametric (pre-trained) knowledge.

## Why Ground the Model?
- Prevents hallucination based on outdated training data
- Makes answers verifiable and citable
- Required for compliance in regulated domains (legal, medical, finance)

## Prompting for Grounding
```
You are a helpful assistant. Answer the user's question using ONLY the context provided below.
If the answer is not in the context, say: "I don't have enough information to answer that."
Do not use your general knowledge.

Context:
"""
{retrieved_chunks}
"""

Question: {user_query}
Answer:
```

## Key Instructions to Include
1. "Use only the context provided" — explicit grounding instruction
2. "If not in the context, say so" — prevents hallucination
3. "Do not make assumptions beyond what is stated" — prevents extrapolation

## Faithfulness vs Relevance
- **Faithfulness**: does the answer accurately reflect what the context says?
- **Relevance**: does the answer address the user's question?

A faithful but irrelevant answer is useless.
A relevant but unfaithful answer is dangerous.

Both must be evaluated separately.

## Failure Mode: Model Ignores Context
Even with grounding instructions, models sometimes fall back to parametric knowledge.
Fixes:
- Repeat grounding instruction at the end of the prompt
- Use lower temperature (more literal)
- Evaluate faithfulness and alert when score drops
- Use structured output to force citation of source text
