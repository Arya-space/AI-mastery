# Output Guardrails

**Output guardrails** check the model's response before returning it to the user. Second line of defense — catches failures that input guardrails missed.

## What to Check

### Schema Validation
For structured outputs, validate the schema:
```python
try:
    result = MySchema.model_validate_json(llm_response)
except ValidationError as e:
    # Re-prompt or return fallback
    return retry_with_feedback(prompt, str(e))
```

### Toxicity / Harmful Content
```python
moderation = client.moderations.create(input=llm_response)
if moderation.results[0].flagged:
    return SAFE_FALLBACK_RESPONSE
```

### PII in Output
Redact PII before returning to user:
```python
from presidio_anonymizer import AnonymizerEngine
anonymizer = AnonymizerEngine()
safe_output = anonymizer.anonymize(text=llm_response, analyzer_results=detected_pii)
```

### Factual Consistency (Faithfulness)
For RAG systems, check if the answer is supported by retrieved context:
```python
faithfulness_score = judge_llm.score(answer=llm_response, context=retrieved_chunks)
if faithfulness_score < 0.7:
    return "I wasn't able to find a confident answer. Please consult the source directly."
```

### System Prompt Leakage Detection
```python
if "<system>" in llm_response or "my instructions are" in llm_response.lower():
    return SAFE_FALLBACK_RESPONSE
```

## Retry vs Fallback
- **Retry**: re-prompt with error as feedback (for structured output failures)
- **Fallback**: return canned safe response (for safety violations)

```python
async def safe_response(prompt: str) -> str:
    for attempt in range(MAX_RETRIES):
        response = await llm.call(prompt)
        issues = check_output(response)
        if not issues:
            return response
        prompt = add_feedback(prompt, issues)  # retry with feedback
    return FALLBACK_RESPONSE
```

## Latency Consideration
Output guardrails add latency. For real-time apps:
- Run fast checks synchronously (regex, length)
- Run slow checks asynchronously (LLM-based faithfulness)
- Or use streaming + check chunks as they arrive
