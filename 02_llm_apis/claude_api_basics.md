# Claude API

> Official docs: [docs.anthropic.com/en/api/messages](https://docs.anthropic.com/en/api/messages)
> Python SDK: [github.com/anthropics/anthropic-sdk-python](https://github.com/anthropics/anthropic-sdk-python)

## Setup

```bash
pip install anthropic
```

```bash
export ANTHROPIC_API_KEY="sk-ant-..."
```

```python
import anthropic
client = anthropic.Anthropic()  # reads ANTHROPIC_API_KEY from environment
```

## Your First API Call

```python
response = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=1024,
    system="You are a helpful assistant.",
    messages=[
        {"role": "user", "content": "What is RAG?"}
    ]
)

print(response.content[0].text)
```

## Key Differences from OpenAI

These will trip you up if you're used to OpenAI:

| | OpenAI | Claude |
|--|--------|--------|
| System prompt | Inside `messages` as `role: "system"` | Top-level `system` parameter |
| Get response text | `response.choices[0].message.content` | `response.content[0].text` |
| `max_tokens` | Optional | **Required** |
| Input tokens | `response.usage.prompt_tokens` | `response.usage.input_tokens` |
| Output tokens | `response.usage.completion_tokens` | `response.usage.output_tokens` |

## The Messages Format

Same `user` / `assistant` pattern as OpenAI, but no `system` role in messages:

```python
messages = [
    {"role": "user", "content": "My name is Alice."},
    {"role": "assistant", "content": "Hi Alice!"},
    {"role": "user", "content": "What's my name?"}
]

response = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=1024,
    system="You are a helpful assistant.",  # always top-level
    messages=messages
)
```

## Key Parameters

| Parameter | What it does | Notes |
|-----------|-------------|-------|
| `model` | Which Claude model | `claude-sonnet-4-6` is the default workhorse |
| `max_tokens` | Max output length | Required — set this or the call fails |
| `system` | System prompt | Top-level, not in messages |
| `temperature` | Randomness 0–1 | Default 1. Lower for factual tasks |
| `stream` | Stream tokens | Use for user-facing interfaces |

Full parameter reference: [docs.anthropic.com/en/api/messages](https://docs.anthropic.com/en/api/messages)

## Reading the Response

```python
response.content[0].text        # the generated text
response.usage.input_tokens     # tokens you sent
response.usage.output_tokens    # tokens generated — more expensive
response.stop_reason            # why generation ended
```

**Stop reasons:**
- `end_turn` — finished naturally
- `max_tokens` — hit the limit, response may be cut off
- `tool_use` — model wants to call a tool

## Streaming

```python
with client.messages.stream(
    model="claude-sonnet-4-6",
    max_tokens=1024,
    messages=[{"role": "user", "content": "Explain embeddings"}]
) as stream:
    for text in stream.text_stream:
        print(text, end="", flush=True)
```

## Available Models

| Model | Best for |
|-------|---------|
| `claude-opus-4-6` | Most capable, complex reasoning |
| `claude-sonnet-4-6` | Best balance of speed and quality |
| `claude-haiku-4-5-20251001` | Fastest, most cost-efficient |

Full model list: [docs.anthropic.com/en/docs/about-claude/models](https://docs.anthropic.com/en/docs/about-claude/models)

## What's Next

- Tool use with Claude: [08 - Tool Calling](../08_tool_calling/what_is_tool_calling.md)
- Managing conversation history: [03 - Memory + Context](../03_memory_context/short_term_memory.md)
- Structured outputs: [05 - Structured Outputs](../05_structured_outputs/why_structured_outputs.md)
