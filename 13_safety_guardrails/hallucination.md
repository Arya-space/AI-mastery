# Hallucination

**Hallucination**: the model generates confident, fluent, plausible-sounding text that is factually incorrect.

## Types
- **Factual hallucination**: false facts ("The Eiffel Tower was built in 1850")
- **Citation hallucination**: references a paper/source that doesn't exist
- **Reasoning hallucination**: logical steps that seem valid but contain errors
- **Context hallucination**: contradicts information provided in the context

## Root Causes
- Training objective: predict next token (fluency), not verify truth
- Model doesn't know what it doesn't know — no uncertainty calibration
- Retrieval failure + generation: model fills gaps with plausible text
- Temperature too high → more creative/wrong outputs

## Mitigations

### RAG + Grounding
Provide relevant context and instruct model to use only that context.
```
"Answer based only on the context provided. If the answer is not in the context, say 'I don't know'."
```

### Low Temperature
Lower temperature = more conservative predictions = less hallucination.
Use `temperature=0` for factual tasks.

### Output Validation
Check the model's output for factual consistency:
```python
def check_faithfulness(answer: str, context: str) -> float:
    prompt = f"Is every claim in this answer supported by the context? Score 0-1.\nContext: {context}\nAnswer: {answer}"
    score = float(llm.call(prompt))
    return score
```

### "I Don't Know" Instruction
Explicitly tell the model it's acceptable to not know:
```
"If you are not confident in the answer, say 'I'm not sure' rather than guessing."
```

## Evaluation
Faithfulness metric: ask a judge model if the answer is supported by the retrieved context.
Track faithfulness score over time — a drop indicates a new failure mode.
