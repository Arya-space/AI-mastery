# Safety Checklist

Pre-launch safety review for AI applications.

## Input Handling
- [ ] Input length limits enforced
- [ ] Moderation API or classifier on user input
- [ ] PII detection on user input
- [ ] Prompt injection detection on user input
- [ ] Rate limiting per user/session

## Output Handling
- [ ] Schema validation for structured outputs
- [ ] Moderation check on LLM output
- [ ] PII redaction in output before returning to user
- [ ] System prompt leakage detection
- [ ] Faithfulness check for RAG answers

## Tool/Agent Safety
- [ ] Least-privilege tools only (no unnecessary write tools)
- [ ] Input validation on every tool call
- [ ] Human approval for irreversible actions
- [ ] Max tool call count per session
- [ ] Code execution sandboxed in container
- [ ] All tool calls logged with full args and results

## Data Access
- [ ] Access control on RAG retrieval (user-scoped)
- [ ] Multi-tenant data isolation verified
- [ ] API keys in secrets manager, not in code
- [ ] .env not committed to git

## Prompt Injection
- [ ] Tested with known injection payloads
- [ ] External data clearly labeled as data (not instructions)
- [ ] Output guardrails catch any successful injections

## Evals
- [ ] Eval dataset includes adversarial safety cases
- [ ] Evals run on every prompt change
- [ ] Evals run in CI pipeline

## Monitoring
- [ ] Logging for all LLM calls (input, output, tokens, latency)
- [ ] Alerts on error rate spikes
- [ ] Alerts on unusual token usage (potential abuse)

## Compliance
- [ ] PII retention policy defined
- [ ] User data deletion (right to forget) implemented
- [ ] Privacy policy covers AI data usage

## Incident Response
- [ ] Process defined for: what to do if model misbehaves in production
- [ ] Kill switch: can disable LLM endpoints without full service outage
- [ ] On-call contacts for security incidents
