# Long-Term Memory

**Long-term memory** = information stored outside the context window, persisted across sessions, retrieved on demand.

## What LLMs Lack Natively
LLMs have no memory across API calls. Every call starts fresh. Long-term memory is an engineering solution built on top.

## External Memory Stores
| Store Type | What It Holds | How Retrieved |
|-----------|--------------|---------------|
| SQL/NoSQL DB | Structured facts, user profiles | Lookup by key/query |
| Vector DB | Unstructured text, conversation history | Semantic search |
| Key-value store (Redis) | Session state, preferences | Exact key lookup |
| File system | Documents, logs | File path lookup |

## Memory Types
- **Episodic**: what happened ("Last session, user asked about X")
- **Semantic**: what you know ("User prefers Python over JavaScript")
- **Procedural**: how to do things ("User's preferred report format")

## Memory Write Strategy
When to save to long-term memory:
- End of conversation session
- When user states a preference explicitly
- When agent completes a significant task
- When a fact is likely to be needed again

```python
async def save_to_memory(user_id: str, key: str, value: str):
    await db.set(f"memory:{user_id}:{key}", value)
    # Also embed and store in vector DB for semantic retrieval
    embedding = await embed(value)
    await vector_db.upsert(id=f"{user_id}_{key}", vector=embedding, metadata={"value": value})
```

## Memory Read Strategy
Before each agent turn, retrieve relevant memories:
```python
async def get_relevant_memories(user_id: str, query: str) -> list[str]:
    # Semantic search over user's stored memories
    query_vec = await embed(query)
    results = await vector_db.search(query_vec, filter={"user_id": user_id}, k=5)
    return [r.metadata["value"] for r in results]
```

## Privacy Considerations
- Always store with user consent
- Implement memory deletion (right to forget)
- Encrypt sensitive memories
- Don't cross-contaminate memories between users
