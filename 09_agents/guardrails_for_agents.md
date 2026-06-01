# Guardrails for Agents

Agents need stricter guardrails than chatbots — they take real-world actions in loops, amplifying any mistake.

## Why Agents Are Higher Risk
- Multi-step: one bad decision cascades across iterations
- Tool calls: can send emails, delete data, make transactions
- Autonomous: may act before you can review

## Input Guardrails
Validate user intent before starting the agent loop:
```python
async def run_agent(user_request: str):
    # Check for harmful intent before doing anything
    safety_check = await classifier.check(user_request)
    if safety_check.is_harmful:
        return "I can't help with that."

    # Check task is within agent's scope
    if not is_in_scope(user_request):
        return "This is outside my capabilities."

    await agent_loop(user_request)
```

## Output Guardrails
Check agent's response before returning to user:
```python
final_response = await agent_loop(goal)

# Validate output
if contains_pii(final_response):
    final_response = redact_pii(final_response)

if not meets_safety_criteria(final_response):
    return "I wasn't able to complete this safely."
```

## Tool Call Validation
Before executing each tool call, validate:
- Are arguments within expected ranges?
- Is this tool appropriate for this task?
- Has the user authorized this type of action?

## Trip-Wire Guardrails
Detect dangerous action sequences:
```python
DANGEROUS_SEQUENCES = [
    ("read_file", "send_email"),  # reading then exfiltrating
    ("delete_record", "delete_record"),  # mass deletion
]

if current_action_sequence in DANGEROUS_SEQUENCES:
    pause_and_alert()
```

## Minimal Footprint Principle
Agents should:
- Request only necessary permissions
- Prefer reversible over irreversible actions
- Confirm before taking irreversible actions
- Stop and ask when uncertain
