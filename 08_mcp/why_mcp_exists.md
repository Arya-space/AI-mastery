# Why MCP Exists

## The M×N Problem
Without a standard:
- M AI apps × N tools = M×N custom integrations
- Every app rewrites the same integrations

With MCP:
- M AI apps + N tools = M+N implementations
- Each app implements MCP client once
- Each tool implements MCP server once
- Any client connects to any server automatically

```
Without MCP:               With MCP:
Claude ←→ GitHub           Claude
Claude ←→ Slack            VSCode      ←→ MCP ←→  GitHub MCP Server
Claude ←→ Notion           Custom App              Slack MCP Server
VSCode ←→ GitHub                                   Notion MCP Server
VSCode ←→ Slack
... (M×N integrations)     (M+N total)
```

## The USB-C Analogy
Before USB-C: every device needed a different cable.
After USB-C: one standard port, any device works.

MCP is USB-C for AI tools.

## Design Goals
1. **Standardization**: one protocol everyone speaks
2. **Discoverability**: clients can ask servers what they expose
3. **Security**: clear boundaries between host and server
4. **Composability**: agents can use multiple servers simultaneously
5. **Reusability**: build a server once, use in Claude Desktop + your app + any MCP host
