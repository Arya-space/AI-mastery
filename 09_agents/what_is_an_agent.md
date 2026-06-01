# What Is an Agent

An **agent** is an LLM-powered system that dynamically decides what actions to take in order to complete a goal, using tools, and running in a loop until done.

## Precise Definition
An agent has:
1. **Goal**: a task to complete
2. **Tools**: capabilities to act on the world
3. **Loop**: iterates until the task is done or it gives up
4. **LLM as the decision engine**: the model decides what to do next

## Agent vs Chatbot
| | Chatbot | Agent |
|--|---------|-------|
| Interaction | One turn | Multi-turn loop |
| Tools | None (or minimal) | Many |
| Goal | Answer a question | Complete a task |
| Autonomy | Low | High |
| State | Stateless | Stateful |

## What Makes a System Agentic
- Model **controls the action sequence** (not the developer)
- System takes **real-world actions** (not just text output)
- **Feedback loop**: actions produce observations, which inform next action

## Role of the LLM
The LLM is the "brain" — it:
- Interprets the goal
- Selects which tool to call next
- Reasons over tool results
- Decides when the task is complete

## Real Use Cases
- Coding agent: reads code, runs tests, fixes bugs, iterates
- Research agent: searches web, reads pages, synthesizes findings
- Data analysis agent: queries DB, creates visualizations, writes report
- Customer support agent: looks up account, resolves issue, sends confirmation
