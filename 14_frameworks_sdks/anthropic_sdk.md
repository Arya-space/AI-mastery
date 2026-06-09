# Anthropic SDK

The official Python SDK for Claude models. Covers messages, streaming, tool use, and the Agent SDK.

## Setup
```bash
pip install anthropic
export ANTHROPIC_API_KEY="sk-ant-..."
```

## Messages API (covered in 04_llm_apis_backend/claude_api_basics.md)
See [Claude API Basics](../04_llm_apis_backend/claude_api_basics.md) for the full reference.

## Claude Agent SDK
```bash
pip install claude-agent-sdk  # or anthropic[agents]
```

```python
from claude_agent_sdk import ClaudeAgent

agent = ClaudeAgent(
    system_prompt="You are a helpful research assistant.",
    tools=[search_web, read_document],
    model="claude-sonnet-4-6"
)

result = agent.run("Research the latest developments in AI agents")
print(result.output)
```

## Key Claude-Specific Features

### Extended Thinking
Claude can "think" before responding — useful for complex reasoning:
```python
response = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=16000,
    thinking={"type": "enabled", "budget_tokens": 10000},
    messages=[{"role": "user", "content": "Solve this complex problem..."}]
)
# response.content includes thinking blocks + answer blocks
for block in response.content:
    if block.type == "thinking":
        print("Thinking:", block.thinking)
    elif block.type == "text":
        print("Answer:", block.text)
```

### Prompt Caching
Cache frequently used context (system prompt, large documents) to reduce cost and latency:
```python
response = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=1024,
    system=[{
        "type": "text",
        "text": very_long_system_prompt,
        "cache_control": {"type": "ephemeral"}  # cache this
    }],
    messages=[{"role": "user", "content": user_query}]
)
# Subsequent calls with same cached content are faster + cheaper
```

### Batch API
Process many requests asynchronously at 50% cost:
```python
batch = client.messages.batches.create(
    requests=[
        {"custom_id": f"req_{i}", "params": {"model": "claude-haiku-4-5-20251001", "max_tokens": 100, "messages": [{"role": "user", "content": text}]}}
        for i, text in enumerate(texts)
    ]
)
# Poll for completion
while batch.processing_status == "in_progress":
    batch = client.messages.batches.retrieve(batch.id)
```

## Claude vs OpenAI SDK Differences
| | Claude (Anthropic) | OpenAI |
|--|-------------------|--------|
| System prompt | Top-level `system=` param | Message with `role: "system"` |
| Response text | `response.content[0].text` | `response.choices[0].message.content` |
| max_tokens | Required | Optional |
| Tool results | Separate content block | Message with `role: "tool"` |
| Streaming | `client.messages.stream()` | `stream=True` parameter |
