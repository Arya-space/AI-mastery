# MCP Client / Server / Host

## Host
The **host** is the AI application the user interacts with.
- Examples: Claude Desktop, VS Code with AI extension, your custom Python app
- Responsible for: managing MCP connections, user permissions, rendering results
- Contains one or more MCP clients

## Client
The **client** is the MCP protocol handler built into the host.
- One client per server connection
- Handles: JSON-RPC communication, capability negotiation, routing tool calls to the right server
- Usually invisible — part of the host's internals

## Server
The **server** is a standalone process that exposes capabilities.
- Examples: GitHub MCP server, Slack MCP server, your custom DB server
- Independent from the host — can be used by any MCP host
- Exposes: tools, resources, and/or prompts

## Relationships
```
Host (1)
 └─ Client A ←→ MCP Server 1 (GitHub)
 └─ Client B ←→ MCP Server 2 (Slack)
 └─ Client C ←→ MCP Server 3 (your DB)
```

The host controls which servers are connected and what permissions each has.

## Real Example: Claude Desktop
- Host: Claude Desktop application
- Clients: one per configured MCP server
- Servers: configured in `~/.claude/claude_desktop_config.json`

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {"GITHUB_TOKEN": "..."}
    }
  }
}
```
