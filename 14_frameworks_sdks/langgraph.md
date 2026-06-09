# LangGraph

LangGraph builds stateful, multi-step agent systems as **graphs** — nodes are actions, edges are transitions. Built on top of LangChain but can be used independently.

## Core Concepts

### Graph = Nodes + Edges
- **Node**: a function that takes state → returns updated state
- **Edge**: connection between nodes (can be conditional)
- **State**: a typed dict shared across all nodes

### Minimal Example
```python
from langgraph.graph import StateGraph, END
from typing import TypedDict

class State(TypedDict):
    messages: list
    result: str

def call_llm(state: State) -> State:
    response = llm.invoke(state["messages"])
    return {"messages": state["messages"] + [response], "result": response.content}

def check_done(state: State) -> str:
    if "DONE" in state["result"]:
        return END
    return "call_llm"  # loop back

graph = StateGraph(State)
graph.add_node("call_llm", call_llm)
graph.set_entry_point("call_llm")
graph.add_conditional_edges("call_llm", check_done)

app = graph.compile()
result = app.invoke({"messages": [{"role": "user", "content": "Research AI trends"}], "result": ""})
```

### Tool-Calling Agent Pattern
```python
from langgraph.prebuilt import create_react_agent

agent = create_react_agent(llm, tools=[search_web, read_file])
result = agent.invoke({"messages": [("user", "Find recent AI papers")]})
```

### Cycles (What Makes LangGraph Powerful)
LangGraph allows **cycles** — the graph can loop back to a previous node. This is how agent loops work: model calls tool → gets result → decides next step → may call another tool → eventually ends.

```
entry → call_llm → should_continue?
                        ↓ yes        ↓ no
                   call_tool       END
                        ↓
                   call_llm  ← (loop back)
```

### Persistence (Memory Across Turns)
```python
from langgraph.checkpoint.memory import MemorySaver

memory = MemorySaver()
app = graph.compile(checkpointer=memory)

# Thread ID keeps conversation state across calls
config = {"configurable": {"thread_id": "user_123"}}
app.invoke({"messages": [("user", "My name is Alice")]}, config=config)
app.invoke({"messages": [("user", "What's my name?")]}, config=config)  # remembers Alice
```

### Human-in-the-Loop
```python
app = graph.compile(interrupt_before=["dangerous_tool"])
# Execution pauses before dangerous_tool node
# Resume after human approval:
app.invoke(None, config=config)  # resumes from checkpoint
```

## LangGraph vs Raw Agent Loop
| | Raw loop | LangGraph |
|--|----------|-----------|
| Simplicity | Simple | More setup |
| State management | Manual | Built-in |
| Persistence | Manual | Built-in checkpointing |
| Visualization | None | Graph visualization |
| Human-in-loop | Manual | Built-in interrupt |
| Multi-agent | Manual | First-class support |

## When to Use LangGraph
- Complex agents with branching logic
- Agents that need memory/persistence across sessions
- Multi-agent systems with handoffs
- Human-in-the-loop workflows
- When you need to visualize and debug the agent flow
