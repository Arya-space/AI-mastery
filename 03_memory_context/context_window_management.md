# Context Window Management

Managing what goes into the context window to maximize usefulness while staying within token limits and cost budgets.

## The Problem
Context window is finite. As conversations grow and agents loop:
- History accumulates → fills context
- Tool results add tokens → fills context
- RAG chunks added → fills context
- Eventually: context overflow or prohibitive cost

## Strategy 1: Summarization
Compress old history into a summary, replace raw messages:
```python
async def compress_history(messages: list, threshold: int = 4000) -> list:
    if count_tokens(messages) < threshold:
        return messages

    old_messages = messages[1:-4]  # keep system + recent
    summary = await llm.call(
        f"Summarize the key facts from this conversation history:\n{format(old_messages)}"
    )
    return [messages[0], {"role": "assistant", "content": f"[Prior context]: {summary}"}] + messages[-4:]
```

## Strategy 2: Selective Retrieval
Don't keep everything in context. Only retrieve what's relevant to the current turn:
```python
relevant_memories = await memory_store.search(current_query, k=3)
context = build_context(system_prompt, relevant_memories, recent_messages[-6:])
```

## Strategy 3: Prompt Compression
Remove redundant/filler text from context before sending:
- Tools like `LLMLingua` compress long documents
- Extractive summarization (keep key sentences only)
- For code: send only relevant functions, not full codebase

## Strategy 4: Tiered Context
Layer context by priority:
```
Always include:  system prompt + last 2 turns (highest priority)
Include if fits: relevant memories + tool history
Include if fits: additional background context
```

## Monitoring Tokens
```python
def log_context_stats(messages: list, model: str):
    tokens = count_tokens(messages, model)
    print(f"Context: {tokens} tokens | Limit: {MODEL_LIMITS[model]} | Usage: {tokens/MODEL_LIMITS[model]:.0%}")
```

## Rule of Thumb
Keep context usage under 60% of the limit to leave room for output tokens and to avoid quality degradation near the limit.
