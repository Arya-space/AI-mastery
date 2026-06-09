# Unsafe Tool Calls

Tools are the highest-risk component in an AI system. They bridge the model's decisions to real-world actions.

## Why Tool Calls Are High Risk
- Write tools are irreversible (delete, send, publish)
- Model can be manipulated into calling tools with wrong args (prompt injection)
- Agent loops can make many tool calls before a human notices
- Failed safety check → real-world harm

## Examples of Dangerous Tool Calls
```
delete_user_account(user_id="12345")   # irreversible
send_email(to="all-staff@...", body="confidential...")   # mass communication
run_sql("DELETE FROM orders WHERE 1=1")   # destroys all data
deploy_to_production(service="payments")   # production impact
charge_card(amount=99999, card_id="...")   # financial harm
```

## Prevention Layers

### 1. Least Privilege
Only expose tools the agent actually needs:
```python
# Bad: give agent access to all tools
tools = ALL_TOOLS

# Good: only tools needed for this task
tools = [search_web, read_document]  # read-only only
```

### 2. Argument Validation
Always validate before executing:
```python
def delete_record(record_id: str):
    if not record_id.isalnum() or len(record_id) > 50:
        raise ValueError("Invalid record ID")
    if not user_owns_record(current_user, record_id):
        raise PermissionError("Not authorized")
    db.delete(record_id)
```

### 3. Confirmation for Irreversible Actions
```python
IRREVERSIBLE_TOOLS = {"delete_record", "send_email", "deploy"}

if tool_name in IRREVERSIBLE_TOOLS:
    confirmed = await ask_user_confirmation(tool_name, args)
    if not confirmed:
        return {"status": "cancelled"}
```

### 4. Sandboxing Code Execution
```python
# Run agent-generated code in isolated container
result = docker_sandbox.run(code, timeout=10, no_network=True)
```

### 5. Rate Limiting
```python
if tool_call_count > MAX_CALLS_PER_SESSION:
    raise SafetyError("Too many tool calls. Session terminated.")
```
