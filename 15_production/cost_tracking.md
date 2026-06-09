# Cost Tracking

LLM API costs can spiral quickly. Track and optimize from day one.

## Cost Structure
| Provider | Input | Output |
|----------|-------|--------|
| GPT-4o | ~$5/1M tokens | ~$15/1M tokens |
| Claude Sonnet 4.x | ~$3/1M tokens | ~$15/1M tokens |
| GPT-4o-mini | ~$0.15/1M tokens | ~$0.60/1M tokens |
| Claude Haiku 4.5 | ~$0.80/1M tokens | ~$4/1M tokens |

Output tokens cost 3–5× more than input tokens.

## Cost Per Request
```python
def estimate_cost(model: str, input_tokens: int, output_tokens: int) -> float:
    prices = {
        "gpt-4o": {"input": 5/1e6, "output": 15/1e6},
        "gpt-4o-mini": {"input": 0.15/1e6, "output": 0.60/1e6},
    }
    p = prices[model]
    return input_tokens * p["input"] + output_tokens * p["output"]

cost = estimate_cost("gpt-4o", 1000, 500)  # ~$0.0125
```

## Tracking Per User
```python
async def tracked_llm_call(user_id: str, prompt: str) -> str:
    response = await llm.call(prompt)
    cost = estimate_cost(model, response.usage.prompt_tokens, response.usage.completion_tokens)
    await db.increment(f"user_cost:{user_id}", cost)
    await db.increment("daily_cost", cost)
    return response.content
```

## Cost Optimization
1. **Choose the right model**: use cheap models (mini/haiku) for simple tasks
2. **Cache responses**: cache identical or near-identical prompts
   ```python
   cache_key = hash(prompt)
   if cached := await redis.get(cache_key):
       return cached
   result = await llm.call(prompt)
   await redis.set(cache_key, result, ex=3600)
   ```
3. **Compress prompts**: remove unnecessary words, use shorter examples
4. **Limit output tokens**: set `max_tokens` to what you actually need
5. **Batch requests**: group small requests where possible

## Budget Alerts
```python
DAILY_BUDGET = 50.0  # $50/day

async def check_budget():
    daily_spend = await db.get("daily_cost")
    if daily_spend > DAILY_BUDGET * 0.8:
        alert(f"80% of daily budget used: ${daily_spend:.2f}")
    if daily_spend > DAILY_BUDGET:
        disable_non_essential_features()
```
