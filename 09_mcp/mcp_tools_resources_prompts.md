# MCP Tools, Resources, and Prompts

MCP servers expose three types of capabilities. The client discovers them on connection.

## Tools
Callable functions the LLM can invoke (like function calling).
- Model-controlled: LLM decides when to call them
- Have input schema (JSON Schema)
- Return text, data, or errors

```json
{
  "name": "create_issue",
  "description": "Create a GitHub issue",
  "inputSchema": {
    "type": "object",
    "properties": {
      "title": {"type": "string"},
      "body": {"type": "string"},
      "repo": {"type": "string"}
    },
    "required": ["title", "repo"]
  }
}
```

## Resources
Data sources the host/user can read. Not called by the LLM autonomously — exposed for the application to read and inject into context.
- Examples: file contents, database rows, API responses
- Addressed by URI: `file:///path/to/doc`, `db://table/row`
- User/host controlled, not model controlled

## Prompts
Reusable prompt templates the server exposes. User can select and invoke them.
- Example: a Git server exposing a "summarize PR" prompt template
- Can accept arguments → fills template with values
- Appear as slash commands or suggestions in the UI

## Discovery
Client asks server at connection:
```
tools/list → list of available tools + schemas
resources/list → list of available resources + URIs
prompts/list → list of available prompt templates
```

## Which to Use
| | Tools | Resources | Prompts |
|--|-------|-----------|---------|
| Triggered by | LLM (model-controlled) | Host/user | User |
| Side effects | Yes | No (read-only) | No |
| Returns | Result | Content | Filled template |
