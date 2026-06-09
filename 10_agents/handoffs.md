# Handoffs

A **handoff** is when one agent transfers control (and context) to another agent.

## Why Handoffs Exist
- No single agent should be good at everything
- Specialized agents have focused system prompts + tool sets
- Handoffs enable routing based on intent/domain

## How a Handoff Works
1. Current agent determines it can't or shouldn't handle the task
2. Agent calls a "handoff" tool (or signals to orchestrator)
3. Target agent receives the conversation history + current state
4. Target agent continues from where the previous left off

## Preserving Context Across Handoffs
Critical: the target agent needs context from the previous agent.
- Pass the full conversation history
- Include a summary of what was done so far
- Pass relevant state (user ID, current task, completed steps)

```python
def handoff_to_billing(context: str, conversation_history: list) -> str:
    billing_agent = Agent(
        name="Billing",
        instructions=f"You are handling a billing issue. Context: {context}"
    )
    return billing_agent.run(conversation_history)
```

## Designing Agent Boundaries
- One agent per domain (billing, support, escalation)
- Clear criteria for when to hand off
- Avoid circular handoffs (A → B → A → B...)
- Define what context each agent needs

## Failure Modes
- **Lost context**: target agent doesn't have enough info to continue
- **Circular handoff**: agents keep passing to each other
- **Unclear boundaries**: agents don't know when to hand off
- **Handoff overhead**: each handoff adds latency + LLM calls

## Fix for Lost Context
Always include a handoff summary:
```
"Handoff from Triage Agent: User reports they were charged twice on March 15.
Account ID: 12345. Previous steps: verified identity, confirmed duplicate charge in DB."
```
