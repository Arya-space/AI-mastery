# Claude API Basics

## Setup
```bash
pip install anthropic
export ANTHROPIC_API_KEY="sk-ant-..."
```
```python
import anthropic
client = anthropic.Anthropic()
```

## Messages API
```python
response = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=1024,
    system="You are a helpful assistant.",  # system is a top-level param, not in messages
    messages=[
        {"role": "user", "content": "What is RAG?"}
    ]
)
print(response.content[0].text)
```

## Key Difference from OpenAI
- System prompt is a **top-level `system` parameter**, not a message with `role: "system"`
- Response is in `response.content[0].text` (not `response.choices[0].message.content`)
- `max_tokens` is **required**

## Multi-turn
```python
messages = [
    {"role": "user", "content": "My name is Alice."},
    {"role": "assistant", "content": "Hi Alice!"},
    {"role": "user", "content": "What's my name?"}
]
```

## Tool Use
```python
response = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=1024,
    tools=[{
        "name": "get_weather",
        "description": "Get current weather",
        "input_schema": {
            "type": "object",
            "properties": {"location": {"type": "string"}},
            "required": ["location"]
        }
    }],
    messages=[{"role": "user", "content": "What's the weather in Paris?"}]
)
# Check if model called a tool
if response.stop_reason == "tool_use":
    tool_call = next(b for b in response.content if b.type == "tool_use")
    print(tool_call.name, tool_call.input)
```

## Streaming
```python
with client.messages.stream(model="claude-sonnet-4-6", max_tokens=1024, messages=[...]) as stream:
    for text in stream.text_stream:
        print(text, end="", flush=True)
```

## Token Usage
```python
response.usage.input_tokens
response.usage.output_tokens
```
