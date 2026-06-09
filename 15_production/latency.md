# Latency

LLM calls are slow. Managing latency is critical for user experience.

## Sources of Latency
1. **Network round-trip**: request to LLM API and back (~50–200ms)
2. **Time to First Token (TTFT)**: how long until model starts generating (~200ms–2s)
3. **Generation time**: sequential token generation (~20–100 tokens/second)
4. **Total response time**: TTFT + (output_tokens / tokens_per_second)

For a 500-token output at 50 tokens/sec: 10 seconds total.

## TTFT vs Total Generation Time
- **TTFT**: time for model to process input + start generating — affected by input length and model size
- **Total time**: dominated by output length

Minimize both: short inputs + short outputs.

## Streaming to Reduce Perceived Latency
Stream tokens as they're generated — user sees output immediately:
```python
async def stream_response(prompt: str):
    async with client.messages.stream(
        model="claude-sonnet-4-6",
        max_tokens=1024,
        messages=[{"role": "user", "content": prompt}]
    ) as stream:
        async for text in stream.text_stream:
            yield text  # send each token to user as it arrives
```
Perceived latency = TTFT (not total time). Makes responses feel much faster.

## Caching
Cache identical prompts:
```python
import hashlib

def prompt_cache_key(model: str, messages: list) -> str:
    content = json.dumps({"model": model, "messages": messages}, sort_keys=True)
    return hashlib.sha256(content.encode()).hexdigest()
```
Effective for: FAQ chatbots, repeated queries, deterministic workflows.

## Model Selection for Latency
| Model | Speed | Quality |
|-------|-------|---------|
| GPT-4o-mini / Claude Haiku | Fast | Good |
| GPT-4o / Claude Sonnet | Medium | Better |
| o1 / thinking models | Slow | Best reasoning |

Use cheaper/faster models where quality allows. Reserve expensive models for complex tasks.

## Profiling
```python
import time

async def timed_llm_call(prompt: str) -> tuple[str, float]:
    start = time.time()
    response = await llm.call(prompt)
    latency_ms = (time.time() - start) * 1000
    print(f"TTFT: {response.ttft_ms}ms | Total: {latency_ms:.0f}ms")
    return response.content, latency_ms
```

## P95 Latency
Track percentile latencies, not just averages. A high P95 means 1 in 20 requests is very slow.
