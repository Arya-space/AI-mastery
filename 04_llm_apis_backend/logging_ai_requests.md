# Logging AI Requests

## What to Log (Every Request)
- Input: prompt text (or hash if PII concerns)
- Output: model response
- Model name + version
- Token counts (input + output)
- Latency (ms)
- Estimated cost
- Correlation ID (to trace across services)
- User ID (anonymized if needed)
- Timestamp
- Success / error status

## Structured Logging
```python
import logging
import json
import time
import uuid

logger = logging.getLogger(__name__)

async def call_llm_with_logging(prompt: str, user_id: str) -> str:
    correlation_id = str(uuid.uuid4())
    start = time.time()

    try:
        response = await client.chat.completions.create(
            model="gpt-4o",
            messages=[{"role": "user", "content": prompt}]
        )
        output = response.choices[0].message.content

        logger.info(json.dumps({
            "event": "llm_request",
            "correlation_id": correlation_id,
            "user_id": user_id,
            "model": "gpt-4o",
            "input_tokens": response.usage.prompt_tokens,
            "output_tokens": response.usage.completion_tokens,
            "latency_ms": int((time.time() - start) * 1000),
            "status": "success"
        }))
        return output

    except Exception as e:
        logger.error(json.dumps({
            "event": "llm_request",
            "correlation_id": correlation_id,
            "status": "error",
            "error": str(e)
        }))
        raise
```

## Privacy Considerations
- Don't log PII (names, emails, SSNs) in plain text
- Hash or truncate sensitive inputs
- Check your compliance requirements (GDPR, HIPAA)
- Log metadata, not necessarily full content

## Tools
- **LangSmith**: tracing + logging for LangChain apps
- **Langfuse**: open-source LLM observability
- **OpenTelemetry**: standard telemetry for any stack
- **Weights & Biases**: traces + evals
