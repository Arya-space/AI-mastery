# Human Approval

Some tool calls should require explicit human confirmation before execution.

## When to Require Approval
- **Irreversible actions**: delete, send, publish, deploy
- **High-impact actions**: financial transactions, sending communications
- **First-time actions**: first time agent uses a write tool in a session
- **Low-confidence situations**: when the agent's interpretation might be wrong

## Approval Pattern
```
Agent decides to call: send_email(to="ceo@company.com", body="...")
    ↓
Pause execution
    ↓
Show user: "I'm about to send this email: [preview]. Approve? [Yes/No/Edit]"
    ↓
User approves → execute
User denies → agent reconsiders or stops
User edits → execute with edits
```

## Implementation
```python
def execute_with_approval(tool_name: str, args: dict, approval_fn) -> dict:
    preview = format_preview(tool_name, args)
    approved = approval_fn(preview)  # blocks until user responds
    if not approved:
        return {"status": "denied", "message": "User declined this action."}
    return execute_tool(tool_name, args)
```

## UX Considerations
- Show a clear, human-readable preview (not raw JSON)
- Allow editing before confirming
- Explain why the action is being taken
- One click to approve, one click to cancel

## Audit Trails
Log every approval decision:
- What was proposed
- Who approved/denied
- Timestamp
- Final outcome

Required for compliance in many enterprise settings.

## "Minimal Footprint" Principle
By default, agents should:
- Prefer reversible over irreversible actions
- Do less and confirm when uncertain
- Request only necessary permissions
