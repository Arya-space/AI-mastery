# Pydantic Validation

**Pydantic** validates Python objects against type definitions at runtime. Essential for LLM outputs.

## Basic Model
```python
from pydantic import BaseModel, Field
from typing import Literal

class SentimentResult(BaseModel):
    label: Literal["positive", "negative", "neutral"]
    confidence: float = Field(ge=0.0, le=1.0)
    reason: str = Field(max_length=200)
```

## Parsing LLM Output
```python
import json

raw = '{"label": "positive", "confidence": 0.9, "reason": "User expressed satisfaction."}'
result = SentimentResult.model_validate_json(raw)
print(result.label)  # "positive"
```

## Instructor Library (Recommended)
`instructor` patches the OpenAI/Anthropic client to return Pydantic models directly:
```python
import instructor
from openai import OpenAI

client = instructor.from_openai(OpenAI())

result = client.chat.completions.create(
    model="gpt-4o",
    response_model=SentimentResult,
    messages=[{"role": "user", "content": "Analyze: 'This is great!'"}]
)
# result is already a SentimentResult — validated
print(result.label)
```

## Error Handling + Re-prompting
```python
from pydantic import ValidationError

try:
    result = SentimentResult.model_validate(data)
except ValidationError as e:
    # Log errors, re-prompt with the validation errors as feedback
    print(e.errors())
```

## Key Fields
- `Field(ge=0, le=1)` — numeric constraints
- `Field(min_length=1)` — string constraints
- `Literal["a", "b"]` — enum-like constraint
- `Optional[str]` — nullable field
