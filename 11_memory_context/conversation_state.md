# Conversation State

**Conversation state** = all information needed to continue a conversation: messages, user data, session context.

## Stateless vs Stateful APIs
- **Stateless** (e.g., raw LLM API): each call is independent, caller sends all context every time
- **Stateful** (your app): you store state server-side, user just sends new message

Most production chat apps are stateful — you store the history and retrieve it per session.

## Session IDs
Each conversation gets a unique session ID. Used to retrieve the right state.

```python
import uuid

def create_session(user_id: str) -> str:
    session_id = str(uuid.uuid4())
    db.set(f"session:{session_id}", {
        "user_id": user_id,
        "messages": [],
        "created_at": now(),
        "metadata": {}
    })
    return session_id

def get_messages(session_id: str) -> list:
    return db.get(f"session:{session_id}")["messages"]

def append_message(session_id: str, role: str, content: str):
    session = db.get(f"session:{session_id}")
    session["messages"].append({"role": role, "content": content})
    db.set(f"session:{session_id}", session)
```

## State Schema
Define what state you track:
```python
class ConversationState(BaseModel):
    session_id: str
    user_id: str
    messages: list[Message]
    current_task: Optional[str]  # for agents
    tool_call_count: int = 0     # for safety limits
    created_at: datetime
    last_active: datetime
```

## Persistence Options
- **Redis**: fast, ephemeral (session data)
- **PostgreSQL**: durable, queryable (important conversations)
- **DynamoDB**: scalable, serverless

## Session Expiry
Always expire sessions:
- Active sessions: keep for duration of use
- Inactive: expire after N hours/days
- Archive or delete on expiry (check your data retention policy)
