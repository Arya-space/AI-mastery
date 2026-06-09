# Read Tools vs Write Tools

## The Distinction
- **Read tools**: fetch/query data, no side effects — safe to run automatically
- **Write tools**: mutate state, send data, trigger actions — require caution

## Examples

| Tool | Type | Why |
|------|------|-----|
| `search_web(query)` | Read | Returns info, changes nothing |
| `get_user_data(id)` | Read | Reads from DB only |
| `calculate(expression)` | Read | Pure computation |
| `send_email(to, body)` | Write | Sends real email |
| `delete_record(id)` | Write | Permanent deletion |
| `create_order(items)` | Write | Real transaction |
| `post_to_slack(msg)` | Write | External side effect |
| `run_sql(query)` | Write (if UPDATE/DELETE) | Modifies data |

## Why This Matters
- Read tools: fail safe — wrong call at worst returns bad data
- Write tools: fail dangerous — wrong call sends emails, deletes data, charges customers

## Safety Layering by Type

**Read tools:**
- Validate inputs
- Log the call
- Run automatically

**Write tools:**
- Validate inputs strictly
- Require human approval for irreversible actions
- Preview before executing ("I'll send this email: [preview]. Confirm?")
- Log everything with full args and outcome
- Implement undo where possible

## Minimal Permissions Principle
Give the agent only the tools it needs for the task.
Don't give a customer support agent access to `delete_account()`.
