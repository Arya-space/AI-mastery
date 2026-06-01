# Agent Evals

Agent evals are harder than prompt evals because you're evaluating a multi-step process, not a single output.

## Why Harder
- No single "correct" answer — many valid action sequences
- Output is a trajectory (series of steps), not just a final response
- Non-deterministic: same goal → different tool call sequences
- Expensive: each eval = many LLM calls

## Task Completion Eval
Did the agent actually accomplish the goal?
```python
def eval_task_completion(goal: str, final_state: dict) -> float:
    # Automated: check if expected artifact exists
    if goal == "create_github_issue":
        return 1.0 if "issue_id" in final_state else 0.0

    # LLM judge: evaluate if goal was achieved
    prompt = f"Was this goal achieved? Goal: {goal}\nFinal output: {final_state['output']}\nAnswer: yes/no"
    return 1.0 if "yes" in llm.call(prompt).lower() else 0.0
```

## Trajectory Evaluation
Evaluate the sequence of steps taken:
- Did the agent call the right tools?
- Did it call them in a reasonable order?
- Did it hallucinate tool calls that don't exist?

```python
expected_tools_used = {"search_web", "read_document"}
actual_tools_used = {step.tool for step in trace.steps if step.type == "tool_call"}
tool_accuracy = len(expected_tools_used & actual_tools_used) / len(expected_tools_used)
```

## Tool Call Accuracy
For each tool call, were the arguments correct?
```python
def eval_tool_args(tool_call, expected_args) -> float:
    correct = sum(1 for k, v in expected_args.items() if tool_call.args.get(k) == v)
    return correct / len(expected_args)
```

## Efficiency
Did the agent take the minimum steps necessary?
```python
efficiency = expected_steps / actual_steps  # 1.0 = optimal
```

## Practical Eval Setup
1. Define 20–50 test tasks with known correct outcomes
2. Run agent on each task
3. Check: task completed? tools used correct? no dangerous actions?
4. Compare baseline vs new version

## Key Insight
Evaluate task completion first. Trajectory and efficiency evals are secondary — only optimize them once you have reliable task completion.
