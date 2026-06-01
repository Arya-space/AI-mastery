# Monitoring

Production AI systems need specialized monitoring beyond standard API monitoring.

## What to Monitor

### LLM-Specific Metrics
- **Input token count**: per request, per user, per day
- **Output token count**: same
- **Latency**: time to first token (TTFT) + total response time
- **Error rate**: 429 (rate limit), 500 (server error), timeout
- **Model used**: track if you have multiple models/versions
- **Cost per request**: calculate from token counts × price per token

### Quality Metrics
- **Eval scores**: run automated evals on sample of production traffic
- **User feedback**: thumbs up/down, explicit ratings
- **Escalation rate**: how often users escalate to human support
- **Task completion rate**: for agents, did the task succeed?

### Business Metrics
- Active users per day
- Conversations per user
- Feature adoption (which tools/capabilities are used)

## Alerting
Set up alerts for:
```python
# Error rate spike
if error_rate_5min > 0.05:  # 5% errors in last 5 minutes
    alert("LLM error rate spike")

# Latency spike
if p95_latency > 10000:  # 10 seconds
    alert("High latency")

# Cost spike
if hourly_cost > BUDGET_THRESHOLD:
    alert("Cost spike — possible abuse")

# Eval score drop
if daily_eval_score < 0.80:
    alert("Quality degradation detected")
```

## Tools
| Tool | Use Case |
|------|----------|
| Langfuse | Open-source LLM observability, traces, evals |
| LangSmith | LangChain ecosystem tracing + evals |
| Helicone | Token/cost tracking, request logging |
| Datadog | General APM + custom LLM metrics |
| Grafana + Prometheus | Self-hosted dashboards |

## Minimal Monitoring Setup
```python
import time
import logging

async def monitored_llm_call(prompt: str) -> str:
    start = time.time()
    try:
        response = await llm.call(prompt)
        tokens = response.usage.total_tokens
        latency = time.time() - start

        metrics.record("llm.tokens", tokens)
        metrics.record("llm.latency", latency * 1000)
        metrics.increment("llm.success")

        return response.content
    except Exception as e:
        metrics.increment("llm.error", tags={"error_type": type(e).__name__})
        raise
```
