# Agentic Patterns

Common reasoning and action patterns used inside agents. These are the "strategies" an agent uses, not the architecture around it.

## 1. ReAct (Reason + Act)
The most common pattern. Model alternates between reasoning and acting.

```
Thought: I need to find the weather in Tokyo.
Action: get_weather(location="Tokyo")
Observation: 25C, sunny
Thought: I have the answer.
Final Answer: The weather in Tokyo is 25C and sunny.
```

Each step: think → act → observe → think again.
Used in: most tool-calling agents by default.

## 2. Plan-and-Execute
Model first creates a full plan, then executes each step.

```
Step 1: Plan
  → "I will: 1) search for competitors, 2) analyze each, 3) write comparison"

Step 2: Execute
  → search_web("competitor A") → analyze → search_web("competitor B") → analyze → write report
```

Better for: long complex tasks where you want the model to think ahead.
Risk: plan can be wrong, hard to adapt mid-execution.

## 3. Reflection / Self-Critique
Agent checks its own output and revises if needed.

```
Draft answer → Critic: "Is this accurate and complete?" → Revise → Final answer
```

```python
draft = agent.generate(goal)
critique = llm.call(f"Critique this answer for accuracy and completeness:\n{draft}")
if "insufficient" in critique.lower():
    final = agent.generate(f"Revise based on this critique:\n{critique}\nOriginal:\n{draft}")
```

Improves quality at the cost of extra LLM calls.

## 4. Tool-Use with Parallel Calls
Call multiple tools simultaneously instead of sequentially.

```python
# Sequential (slow): 3 × latency
weather_tokyo = get_weather("Tokyo")
weather_paris = get_weather("Paris")
weather_london = get_weather("London")

# Parallel (fast): 1 × latency
results = await asyncio.gather(
    get_weather("Tokyo"),
    get_weather("Paris"),
    get_weather("London")
)
```

Most modern LLMs support parallel tool calls natively — the model returns multiple tool calls in one response.

## 5. Subagent Delegation
Orchestrator breaks task into subtasks, delegates each to a specialist agent.

```
Orchestrator: "Write a competitive analysis report"
  → Research Agent: gather data on competitors
  → Analysis Agent: identify patterns and insights
  → Writing Agent: produce final formatted report
Orchestrator: merge outputs → final report
```

## 6. Memory-Augmented Agent
Agent reads from and writes to long-term memory to maintain context across sessions.

```
Start of turn:
  → retrieve relevant memories for current query

End of turn:
  → extract key facts from this conversation
  → save to memory store

Next session:
  → relevant memories surface automatically
```

## 7. Iterative Refinement
Agent produces output, evaluates it, refines, repeat.

```
generate → evaluate (score < threshold?) → refine → evaluate → ... → done
```

Used for: code generation (write → test → fix), creative writing, complex analysis.

## Choosing a Pattern

| Task | Pattern |
|------|---------|
| Simple tool use | ReAct |
| Complex multi-step task | Plan-and-Execute |
| High-quality output required | Reflection |
| Independent subtasks | Parallel tool calls |
| Multi-domain task | Subagent delegation |
| Personalized experience | Memory-augmented |
| Quality-critical output | Iterative refinement |
