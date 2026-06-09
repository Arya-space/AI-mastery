# MCP vs REST API

## REST API
- Designed for human developers to call from code
- Requires reading documentation to know what's available
- Fixed endpoints, request/response format
- No built-in concept of AI tool calling

## MCP Server
- Designed for AI systems to call
- **Self-describing**: client can discover all tools, schemas, descriptions automatically
- Built on JSON-RPC, not HTTP REST
- LLM reads tool descriptions to decide what to call

## MCP Can Wrap a REST API
A common pattern: build an MCP server that internally calls your REST API.
```
LLM → MCP client → MCP server → calls REST API internally → returns result
```
This lets existing APIs become AI-accessible without modifying the API itself.

## When to Use a REST API Call from a Tool
If you control both sides:
- Simple tool calls that call an internal REST API → fine as raw tool calling
- No need for MCP if you're not building a reusable server

## When MCP Is Better Than a Direct API Call
- When you want the tool to be reusable across multiple AI apps
- When you need the AI to discover capabilities dynamically
- When you're building a tool ecosystem, not a one-off integration

## Discovery: The Key MCP Advantage
REST APIs: you read docs → hardcode endpoints in your app.
MCP: client connects → asks "what can you do?" → server responds with full schema → LLM understands capabilities automatically.
