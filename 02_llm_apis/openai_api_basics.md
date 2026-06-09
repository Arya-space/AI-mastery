# OpenAI API

> Official docs: [platform.openai.com/docs/api-reference/chat](https://platform.openai.com/docs/api-reference/chat)
> Python SDK: [github.com/openai/openai-python](https://github.com/openai/openai-python)

## Setup

```bash
pip install openai
```

Never hardcode your API key. Store it as an environment variable:
```bash
export OPENAI_API_KEY="sk-..."
```

```python
from openai import OpenAI
client = OpenAI()  # automatically reads OPENAI_API_KEY from environment
```

## Your First API Call

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

## The Messages Format

The `messages` list is how you pass context to the model. Three roles:

| Role | What it's for |
|------|--------------|
| `system` | Your instructions — persona, rules, format, constraints |
| `user` | What the user sent |
| `assistant` | The model's previous responses (used for multi-turn chat) |

For multi-turn conversations, you append each new exchange to the messages list:

```python
messages = [
    {"role": "system", "content": "You are a helpful assistant."},
    {"role": "user", "content": "My name is Alice."},
    {"role": "assistant", "content": "Hi Alice! How can I help?"},
    {"role": "user", "content": "What's my name?"}  # model knows — it's in context
]
```

> The model has no memory between calls. Every turn you must pass the full conversation history yourself. This is covered in detail in [03 - Memory + Context](../03_memory_context/short_term_memory.md).

## Key Parameters

| Parameter | What it does | When to use |
|-----------|-------------|-------------|
| `model` | Which model to call | `gpt-4o` for best quality, `gpt-4o-mini` for speed/cost |
| `temperature` | Randomness 0–2. 0 = deterministic, 1 = default | Low (0–0.3) for factual tasks, higher for creative |
| `max_tokens` | Max output length in tokens | Set high enough so responses aren't cut off |
| `stream` | Stream tokens as generated | `True` for any user-facing UI |
| `response_format` | `{"type": "json_object"}` for JSON mode | When you need structured output |

Full parameter reference: [platform.openai.com/docs/api-reference/chat/create](https://platform.openai.com/docs/api-reference/chat/create)

## Reading the Response

```python
response.choices[0].message.content   # the generated text
response.usage.prompt_tokens           # input tokens (what you sent)
response.usage.completion_tokens       # output tokens (what was generated)
response.usage.total_tokens            # total — this is what you're billed for
response.choices[0].finish_reason      # why generation stopped
```

**Stop reasons:**
- `stop` — finished naturally
- `length` — hit `max_tokens` limit, response may be cut off
- `tool_calls` — model wants to call a tool

## Streaming

Use streaming for any interface where the user watches the response appear:

```python
stream = client.chat.completions.create(
    model="gpt-4o",
    messages=[{"role": "user", "content": "Explain embeddings"}],
    stream=True
)

for chunk in stream:
    print(chunk.choices[0].delta.content or "", end="", flush=True)
```

## What's Next

- Tool calling (function calling) is covered in [08 - Tool Calling](../08_tool_calling/what_is_tool_calling.md)
- Managing long conversations: [03 - Memory + Context](../03_memory_context/short_term_memory.md)
- Getting structured JSON output: [05 - Structured Outputs](../05_structured_outputs/why_structured_outputs.md)
