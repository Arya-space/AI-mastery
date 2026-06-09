# Agent Types

## Single Agent with Tools
Simplest pattern. One LLM + a set of tools. The model calls tools and loops until done.

Best for: focused tasks with a small, well-defined tool set.

## Multi-Agent: Orchestrator + Subagents
An **orchestrator** agent breaks the goal into subtasks and delegates to **subagents**.

```
Orchestrator: "Research and write a report on X"
    ├─ Research Agent: searches web, reads articles
    ├─ Analysis Agent: processes data, finds trends
    └─ Writing Agent: produces final report
```

Benefits: parallelism, specialization, each agent has a focused context.
Risks: handoff errors, orchestrator losing track of state.

## Parallel Agents
Multiple agents run simultaneously on independent subtasks, results merged.
```python
results = await asyncio.gather(
    agent_a.run("Analyze Q1 data"),
    agent_b.run("Analyze Q2 data"),
    agent_c.run("Analyze Q3 data"),
)
final = synthesize(results)
```

## Specialized Agents with Handoffs
Each agent has a specific domain:
- Triage agent → routes to: billing agent, tech support agent, escalation agent
- Each has domain-specific tools and system prompt

## Tool-Using vs Code-Executing Agents
- **Tool-using**: calls predefined functions (safer, more controlled)
- **Code-executing**: writes and runs code dynamically (more powerful, higher risk)

## OpenAI Agents SDK Pattern
```python
from agents import Agent, handoff

billing_agent = Agent(name="Billing", instructions="Handle billing questions", tools=[...])
support_agent = Agent(name="Support", instructions="Handle technical issues", tools=[...])

triage_agent = Agent(
    name="Triage",
    instructions="Route to the right specialist",
    handoffs=[billing_agent, support_agent]
)
```
