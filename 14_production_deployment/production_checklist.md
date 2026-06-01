# Production Checklist

Pre-launch review for AI services.

## Security
- [ ] API keys in secrets manager, not in code or env files in prod
- [ ] Input guardrails: moderation, PII detection, length limits
- [ ] Output guardrails: toxicity check, PII redaction, schema validation
- [ ] Prompt injection tested with known payloads
- [ ] Tool calls validated and least-privilege only
- [ ] Multi-user data isolation verified (no cross-user data leakage)
- [ ] HTTPS only (no HTTP endpoints)
- [ ] Rate limiting per user/IP

## Performance
- [ ] Async endpoints (no blocking calls in API handlers)
- [ ] Streaming implemented for long responses
- [ ] Response caching for repeated queries
- [ ] Max token limits set on all LLM calls
- [ ] Load tested at expected peak traffic
- [ ] P95 latency measured and acceptable

## Reliability
- [ ] Retry logic with exponential backoff on LLM API calls
- [ ] Fallback model or response if primary model fails
- [ ] Health endpoint (`/health`) returns 200 when healthy
- [ ] Graceful error messages (no raw stack traces to users)
- [ ] Session state in external store (Redis/DB), not in-memory
- [ ] Stateless app containers (can be restarted/scaled freely)

## Cost
- [ ] Per-request token counts logged
- [ ] Daily/monthly cost estimated and within budget
- [ ] Budget alerts configured
- [ ] Cheapest model used for tasks that don't need the best

## Observability
- [ ] Structured logging for all LLM calls (input hash, output, tokens, latency)
- [ ] Error rate alerting
- [ ] Latency alerting (P95 threshold)
- [ ] Cost spike alerting
- [ ] Eval scores tracked on production sample

## Evals
- [ ] Eval dataset covers key use cases
- [ ] Evals run in CI pipeline
- [ ] Eval score above minimum threshold before deployment
- [ ] Safety evals included

## Runbook
- [ ] Documented: how to roll back a bad deployment
- [ ] Documented: how to disable LLM feature under incident
- [ ] Documented: on-call rotation and escalation path
- [ ] Incident postmortem template ready
