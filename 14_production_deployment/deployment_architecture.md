# Deployment Architecture

## Deployment Patterns

### Serverless (Cloud Functions / Lambda)
- Scales to zero, pay per call
- Good for: low/medium traffic, event-driven
- Bad for: high sustained traffic (cold starts), long-running agents
- Examples: AWS Lambda, Google Cloud Run, Vercel Functions

### Container (ECS, GKE, Kubernetes)
- Always-on containers, horizontal scaling
- Good for: high traffic, consistent latency, complex apps
- Examples: AWS ECS, Google GKE, self-hosted Kubernetes

### VM
- Full control, good for GPU-heavy workloads (self-hosted models)
- Higher ops burden

## Stateless API Design
**Rule**: your API server must be stateless.
- No session data in memory (process can be killed/scaled anytime)
- Store all state in external DB (Redis, PostgreSQL)
- Any request can be served by any instance

```python
# BAD: state in memory
session_storage = {}  # dies when container restarts

# GOOD: state in Redis
async def get_session(session_id: str):
    return await redis.get(f"session:{session_id}")
```

## Architecture for a Production AI App
```
User
  ↓
Load Balancer (distributes traffic)
  ↓
API Containers (stateless, auto-scaled 2–10 instances)
  ↓                    ↓
LLM API (OpenAI)    Redis (session state)
                        ↓
                    PostgreSQL (persistent data)
                        ↓
                    Vector DB (Pinecone/pgvector)
```

## Handling Async Workloads
LLM calls can take 5–30 seconds. Don't block HTTP requests:
```
User → POST /tasks → returns {task_id: "abc"} immediately
Background worker runs LLM task
User → GET /tasks/abc → returns result when ready
```
Use: Celery + Redis, AWS SQS + Lambda, or async job queues.

## Scaling
- LLM API calls are I/O-bound → async Python handles many concurrent requests
- Scale horizontally (more containers) not vertically (bigger server)
- Rate limit per user to prevent runaway costs
