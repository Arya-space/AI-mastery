# OpenAI API Basics

## Setup
```bash
pip install openai
export OPENAI_API_KEY="sk-..."
```
```python
from openai import OpenAI
client = OpenAI()  # reads OPENAI_API_KEY from env
```

## Chat Completions
```python
response = client.chat.completions.create(
    model="gpt-4o",
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "What is RAG?"}
    ],
    temperature=0.7,
    max_tokens=500
)
print(response.choices[0].message.content)
```

## Messages Format
- `system`: developer instructions (persona, rules, format)
- `user`: user's input
- `assistant`: model's previous responses (for multi-turn)

```python
# Multi-turn conversation
messages = [
    {"role": "system", "content": "You are a helpful assistant."},
    {"role": "user", "content": "My name is Alice."},
    {"role": "assistant", "content": "Hi Alice! How can I help?"},
    {"role": "user", "content": "What's my name?"}
]
```

## Key Parameters
| Parameter | What it does |
|-----------|-------------|
| `model` | Which model to use |
| `temperature` | Randomness (0–2) |
| `max_tokens` | Max output tokens |
| `stream` | Stream tokens as they're generated |
| `response_format` | `{"type": "json_object"}` for JSON mode |

## Response Structure
```python
response.choices[0].message.content      # text output
response.usage.prompt_tokens             # input tokens used
response.usage.completion_tokens         # output tokens used
response.usage.total_tokens              # total
```

## Streaming
```python
stream = client.chat.completions.create(model="gpt-4o", messages=[...], stream=True)
for chunk in stream:
    print(chunk.choices[0].delta.content or "", end="")
```
