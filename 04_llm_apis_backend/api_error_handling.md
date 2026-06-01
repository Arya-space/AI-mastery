# API Error Handling

## Common LLM API Errors
| Error | Cause | Fix |
|-------|-------|-----|
| `RateLimitError` (429) | Too many requests | Retry with backoff |
| `APITimeoutError` | Request took too long | Retry, set shorter timeout |
| `ContextWindowExceeded` | Prompt too long | Truncate input |
| `AuthenticationError` (401) | Bad API key | Check env var |
| `ServiceUnavailableError` (503) | Provider outage | Retry with backoff |

## Retry with Exponential Backoff
```python
import time
import random
from openai import RateLimitError, APITimeoutError

def call_with_retry(func, max_retries=3):
    for attempt in range(max_retries):
        try:
            return func()
        except (RateLimitError, APITimeoutError) as e:
            if attempt == max_retries - 1:
                raise
            wait = (2 ** attempt) + random.uniform(0, 1)
            time.sleep(wait)
```

## Using tenacity (recommended)
```python
from tenacity import retry, stop_after_attempt, wait_exponential, retry_if_exception_type

@retry(
    stop=stop_after_attempt(3),
    wait=wait_exponential(multiplier=1, min=1, max=10),
    retry=retry_if_exception_type((RateLimitError, APITimeoutError))
)
async def call_llm(prompt):
    return await client.chat.completions.create(...)
```

## Context Window Overflow
```python
import tiktoken

def truncate_to_fit(text: str, max_tokens: int, model: str = "gpt-4o") -> str:
    enc = tiktoken.encoding_for_model(model)
    tokens = enc.encode(text)
    if len(tokens) > max_tokens:
        tokens = tokens[:max_tokens]
    return enc.decode(tokens)
```

## User-Facing Errors
Never surface raw API errors to users:
```python
try:
    result = await call_llm(prompt)
except Exception as e:
    logger.error(f"LLM call failed: {e}")
    return {"error": "Service temporarily unavailable. Please try again."}
```

## Fallback Strategy
Primary model fails → fallback to cheaper/different model → return cached response → return graceful error.
