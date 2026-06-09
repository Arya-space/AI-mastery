# Short-Term Memory

**Short-term memory** = the context window. Everything in the current LLM call. Forgotten when the call ends.

## The Context Window as Memory
The model's working memory is the messages list passed in each API call:
```python
messages = [
    {"role": "system", "content": "You are a helpful assistant."},
    {"role": "user", "content": "My name is Alice."},
    {"role": "assistant", "content": "Hi Alice!"},
    {"role": "user", "content": "What's my name?"}  # model can answer from context
]
```
The model knows Alice's name because it's in context. Remove that message → model forgets.

## Managing Conversation History
For multi-turn chat, you accumulate messages. Must trim before hitting the context limit.

### Sliding Window
Keep only the last N messages:
```python
MAX_MESSAGES = 20

def trim_history(messages: list, max_messages: int = MAX_MESSAGES) -> list:
    system = [m for m in messages if m["role"] == "system"]
    history = [m for m in messages if m["role"] != "system"]
    return system + history[-max_messages:]
```

### Token-Based Trim
Count tokens, drop oldest non-system messages until under limit:
```python
def trim_to_token_limit(messages: list, max_tokens: int) -> list:
    while count_tokens(messages) > max_tokens:
        # Remove oldest user/assistant pair (keep system prompt)
        messages = [messages[0]] + messages[3:]  # drop messages[1] and [2]
    return messages
```

## Summarization of History
When trimming loses important context, summarize before dropping:
```python
summary = llm.call(f"Summarize the key points from this conversation: {old_history}")
# Replace old history with summary message
messages = [system_prompt, {"role": "assistant", "content": f"Previous context: {summary}"}] + recent_messages
```

## Tool Results in Short-Term Memory
Tool call results also consume context. For agents with many tool calls, results accumulate quickly → monitor total tokens per loop iteration.
