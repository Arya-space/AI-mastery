# MCP Architecture

## Full Picture
```
Host Application (e.g., Claude Desktop)
    └─ MCP Client
         │  JSON-RPC over stdio or SSE
         └─ MCP Server (separate process)
               ├─ Tools (callable functions)
               ├─ Resources (data sources)
               └─ Prompts (templates)
```

One host can have multiple MCP clients → multiple MCP servers simultaneously.

## Transport Layer
**stdio**: server is a local subprocess. Client writes JSON to stdin, reads from stdout.
- Simple, fast, used for local servers
- Claude Desktop config: `{"command": "python", "args": ["server.py"]}`

**SSE (Server-Sent Events)**: server runs as HTTP service, client connects over HTTP.
- Used for remote / cloud servers
- Supports multiple concurrent clients

## Protocol Messages (JSON-RPC 2.0)
```
Client → Server:  {"method": "initialize", ...}
Server → Client:  {"result": {"protocolVersion": "2024-11-05", "capabilities": {...}}}

Client → Server:  {"method": "tools/list"}
Server → Client:  {"result": {"tools": [{"name": "...", "description": "...", "inputSchema": {...}}]}}

Client → Server:  {"method": "tools/call", "params": {"name": "get_weather", "arguments": {...}}}
Server → Client:  {"result": {"content": [{"type": "text", "text": "25°C, sunny"}]}}
```

## Connection Lifecycle
1. Host spawns server process (stdio) or connects to URL (SSE)
2. Client sends `initialize` → server responds with capabilities
3. Client lists tools/resources/prompts
4. Client passes available tools to LLM
5. LLM requests tool call → client routes to server → server executes → result returned
6. Server shuts down when connection closes
