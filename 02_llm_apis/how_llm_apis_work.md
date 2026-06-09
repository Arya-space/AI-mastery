# How LLM APIs Work

When you build an AI application, you rarely run the model yourself. You call a hosted model over HTTP — the same way you'd call any web API. The model lives on Anthropic's or OpenAI's servers, and you send it messages and get responses back.

## The Request Flow

![Anthropic API Request Flow](../assets/images/anthropic_api.png)

The flow for every LLM API call:

1. **Your app** (web, mobile, CLI) sends a request to your server
2. **Your server** uses the SDK (Anthropic, OpenAI) to call the model API
3. **The model API** processes the request and runs inference
4. **The response** travels back through your server to your app

> You never call the LLM API directly from a frontend. Your API key would be exposed. Always go through your own server.

## What You Send (The Request)

Every LLM API call has the same basic structure:

```python
{
    "model": "which model to use",
    "messages": [
        {"role": "system", "content": "instructions for the model"},
        {"role": "user", "content": "what the user said"}
    ],
    "max_tokens": 1024,      # how long the response can be
    "temperature": 0.7       # how creative/random the output is
}
```

## What You Get Back (The Response)

Every response contains three key things:

| Field | What it is |
|-------|-----------|
| **Message** | The generated text from the model |
| **Usage** | Count of input tokens and output tokens used |
| **Stop reason** | Why generation ended (`end_turn`, `max_tokens`, `tool_use`) |

Understanding stop reasons matters:
- `end_turn` — model finished naturally. Good.
- `max_tokens` — response was cut off. Increase `max_tokens` or the output is incomplete.
- `tool_use` — model wants to call a tool. Your code needs to handle this.

## Sync vs Streaming

**Default (sync):** wait for the full response, then get it all at once.
```
send request → wait 2-5 seconds → receive full response
```

**Streaming:** receive tokens as they're generated, one at a time.
```
send request → token by token → response builds live in the UI
```

Use streaming for any user-facing chat interface. Use sync for background tasks.

## Tokens = Cost + Speed

Every API call is billed in tokens:
- **Input tokens** — everything you send (system prompt + conversation history + user message)
- **Output tokens** — what the model generates (more expensive, ~3-5x per token)

This means long system prompts and long conversation histories add up fast. You'll learn to manage this in [03 - Memory + Context](../03_memory_context/context_window.md).

## Next

- [OpenAI API](openai_api_basics.md) — how to make your first call with the OpenAI SDK
- [Claude API](claude_api_basics.md) — same concepts, different SDK syntax
