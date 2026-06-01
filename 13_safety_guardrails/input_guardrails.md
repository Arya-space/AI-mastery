# Input Guardrails

**Input guardrails** validate user input before it reaches the LLM. First line of defense.

## What to Check

### Topic / Intent Classification
Is the request within scope? Is it harmful?
```python
def check_input_safety(user_input: str) -> SafetyResult:
    # Use a classifier model (e.g., Llama Guard, OpenAI Moderation)
    result = moderation_api.check(user_input)
    return SafetyResult(
        is_safe=result.safe,
        category=result.flagged_category  # e.g., "violence", "hate_speech"
    )
```

### Toxicity / Harmful Content Detection
```python
from openai import OpenAI
client = OpenAI()

response = client.moderations.create(input=user_input)
if response.results[0].flagged:
    return "I can't help with that."
```

### PII Detection
Don't let PII accidentally flow into logs or external systems:
```python
from presidio_analyzer import AnalyzerEngine
analyzer = AnalyzerEngine()
entities = analyzer.analyze(text=user_input, language="en")
pii_found = [e.entity_type for e in entities]
if "CREDIT_CARD" in pii_found:
    return "Please don't share payment information here."
```

### Length and Format Validation
```python
if len(user_input) > MAX_INPUT_LENGTH:
    return "Input too long. Please be more concise."

if not user_input.strip():
    return "Please enter a message."
```

### Prompt Injection Detection
```python
INJECTION_SIGNALS = ["ignore previous", "disregard instructions", "new instructions:"]
if any(sig in user_input.lower() for sig in INJECTION_SIGNALS):
    log_security_event("possible_injection", user_input)
    return "Invalid input."
```

## Implementation Pattern
```python
async def handle_request(user_input: str) -> str:
    # Run all input checks
    if not is_within_length(user_input):
        return error("Input too long")
    if not passes_moderation(user_input):
        return error("Content policy violation")
    if has_pii(user_input):
        return error("Don't share personal information")

    # Only reach LLM if all checks pass
    return await call_llm(user_input)
```

## Performance Tip
Run input guardrails in parallel if using multiple checks. Don't block on one slow classifier before running others.
