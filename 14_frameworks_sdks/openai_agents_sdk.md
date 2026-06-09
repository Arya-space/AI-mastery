# OpenAI Agents SDK

The official OpenAI SDK for building agents with tools, handoffs, and tracing. Clean, minimal, production-ready.

## Install
```bash
pip install openai-agents
```

## Core Concepts

### Agent
```python
from agents import Agent, Runner

agent = Agent(
    name="Assistant",
    instructions="You are a helpful assistant. Be concise.",
    model="gpt-4o"
)

result = Runner.run_sync(agent, "What is RAG?")
print(result.final_output)
```

### Tools
```python
from agents import Agent, Runner, function_tool

@function_tool
def get_weather(location: str) -> str:
    """Get current weather for a city."""
    return f"25C, sunny in {location}"

@function_tool
def search_web(query: str) -> str:
    """Search the web for information."""
    # call search API...
    return "search results..."

agent = Agent(
    name="Research Agent",
    instructions="Use tools to answer questions accurately.",
    tools=[get_weather, search_web],
    model="gpt-4o"
)

result = Runner.run_sync(agent, "What's the weather in Tokyo?")
```

### Handoffs
Transfer control from one agent to another:
```python
billing_agent = Agent(
    name="Billing",
    instructions="Handle billing and payment questions.",
    tools=[lookup_invoice, process_refund]
)

support_agent = Agent(
    name="Support",
    instructions="Handle technical support questions.",
    tools=[check_system_status, create_ticket]
)

triage_agent = Agent(
    name="Triage",
    instructions="Route to the right specialist based on the user's question.",
    handoffs=[billing_agent, support_agent]
)

result = Runner.run_sync(triage_agent, "I was charged twice last month")
# Triage agent hands off to billing_agent automatically
```

### Guardrails
Input and output validation:
```python
from agents import Agent, InputGuardrail, GuardrailFunctionOutput
from pydantic import BaseModel

class SafetyCheck(BaseModel):
    is_safe: bool
    reason: str

async def safety_guardrail(ctx, agent, input) -> GuardrailFunctionOutput:
    result = await Runner.run(
        Agent(name="Safety", instructions="Check if input is safe", output_type=SafetyCheck),
        input
    )
    return GuardrailFunctionOutput(
        output_info=result.final_output,
        tripwire_triggered=not result.final_output.is_safe
    )

agent = Agent(
    name="Assistant",
    instructions="Be helpful.",
    input_guardrails=[InputGuardrail(guardrail_function=safety_guardrail)]
)
```

### Tracing
Built-in — every run is automatically traced:
```python
# Traces appear in the OpenAI dashboard
# Or export to custom processor:
from agents.tracing import set_trace_processors

set_trace_processors([my_custom_processor])
```

### Structured Output
```python
from pydantic import BaseModel

class Report(BaseModel):
    summary: str
    key_points: list[str]
    confidence: float

agent = Agent(
    name="Analyst",
    instructions="Analyze and return structured report.",
    output_type=Report
)
result = Runner.run_sync(agent, "Analyze Q3 performance")
report: Report = result.final_output  # already validated Pydantic model
```

## When to Use OpenAI Agents SDK
- Building agents in the OpenAI ecosystem
- Need clean handoff patterns
- Want built-in tracing without extra setup
- Simpler than LangGraph for most agent use cases
