# JSON Outputs

## Prompting for JSON
Explicitly request JSON and show the schema in the prompt:
```
Return your answer as a JSON object with this structure:
{
  "label": "positive" | "negative" | "neutral",
  "confidence": float between 0 and 1,
  "reason": "one sentence"
}
Return only valid JSON, no other text.
```

## OpenAI JSON Mode
Forces the model to return valid JSON (but you still define the structure via prompt):
```python
response = client.chat.completions.create(
    model="gpt-4o",
    response_format={"type": "json_object"},
    messages=[{"role": "user", "content": "Extract name and age as JSON: John is 30."}]
)
import json
data = json.loads(response.choices[0].message.content)
```

## OpenAI Structured Outputs (stronger guarantee)
Uses a JSON schema to constrain output — guaranteed to match schema:
```python
from pydantic import BaseModel

class Person(BaseModel):
    name: str
    age: int

response = client.beta.chat.completions.parse(
    model="gpt-4o",
    messages=[...],
    response_format=Person,
)
person = response.choices[0].message.parsed  # Already a Person object
```

## Handling Malformed JSON
Always wrap parsing in try/except:
```python
try:
    data = json.loads(raw_output)
except json.JSONDecodeError:
    # re-prompt or log and return error
    pass
```

## When JSON Mode Isn't Enough
JSON mode only guarantees valid JSON syntax — not that the keys/values are correct.
Use Pydantic validation on top to enforce the schema.
