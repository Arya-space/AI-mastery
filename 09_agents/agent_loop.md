# The Agent Loop

The **agent loop** is the core execution pattern: the model reasons and acts repeatedly until the task is done.

## The Loop
```
1. PERCEIVE: receive goal + current state (messages, tool results)
2. REASON: model decides what to do next
3. ACT: call a tool (or return final answer)
4. OBSERVE: receive tool result
5. REPEAT: go back to step 1 with updated state
6. STOP: when model outputs final answer or hits limit
```

## Minimal Code Example
```python
async def agent_loop(user_goal: str, tools: list, max_iterations: int = 10) -> str:
    messages = [{"role": "user", "content": user_goal}]

    for i in range(max_iterations):
        response = await llm.call(messages=messages, tools=tools)

        # Check if done
        if response.stop_reason == "end_turn":
            return response.text

        # Execute tool calls
        for tool_call in response.tool_calls:
            result = await execute_tool(tool_call.name, tool_call.arguments)
            messages.append({"role": "tool", "content": result, "tool_call_id": tool_call.id})

        messages.append({"role": "assistant", "content": response.content})

    return "Max iterations reached. Task incomplete."
```

## Loop Termination
- **Success**: model outputs final answer with no tool calls
- **Max iterations**: safety limit hit (always set this)
- **Error**: tool call fails, model decides to stop
- **Human interrupt**: user cancels

## Tracking State
State lives in the messages list. Each iteration adds:
- Model's reasoning + tool call request
- Tool execution result

The model sees the full history on each iteration.

## Preventing Infinite Loops
- Always set `max_iterations` (typically 10–50 depending on task)
- Track tool call count separately
- Monitor total token cost per session
- Alert if loop runs longer than expected
