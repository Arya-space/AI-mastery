# Tracing

**Tracing** records the full execution of an agent — every LLM call, tool call, input, output, and timing. Essential for debugging.

## Why Tracing Matters for Agents
A failed agent gives you only the final error. Without a trace:
- You can't see which tool failed
- You can't see what the model was thinking
- You can't reproduce the failure

With a trace: you see every step, every decision, every result.

## What to Trace
- Every LLM call: prompt, response, model, token count, latency
- Every tool call: name, arguments, result, latency, success/fail
- Agent decisions: why the model chose this tool
- Full message history at each step
- Total tokens + cost for the session
- User ID + session ID for correlation

## Trace Structure
```python
trace = {
    "session_id": "abc123",
    "user_id": "user_456",
    "goal": "Research competitors and write summary",
    "steps": [
        {
            "step": 1,
            "type": "llm_call",
            "prompt_tokens": 500,
            "completion_tokens": 200,
            "latency_ms": 1200,
            "tool_calls_requested": ["search_web"]
        },
        {
            "step": 2,
            "type": "tool_call",
            "tool": "search_web",
            "args": {"query": "competitor analysis AI tools 2024"},
            "result": "...",
            "latency_ms": 800
        }
    ],
    "total_steps": 8,
    "total_tokens": 4500,
    "final_output": "...",
    "status": "success"
}
```

## Tools
- **LangSmith**: tracing for LangChain/custom agents
- **Langfuse**: open-source, provider-agnostic
- **OpenAI Agents SDK**: built-in tracing
- **Weights & Biases (W&B)**: traces + evals

## Using Traces to Debug
1. Find the failing session by session ID
2. Look at the step where output diverges from expected
3. Check: was the right tool called? Were args correct? Did tool succeed?
4. Check: did model misinterpret the tool result?
5. Fix: adjust system prompt, fix tool schema, or fix tool implementation
