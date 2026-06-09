# MCP vs Tool Calling

## What They Have in Common
- Both let an LLM invoke external functions
- Both use JSON schemas to define parameters
- Both follow: model requests call → code executes → result returned to model

## Key Differences
| | Raw Tool Calling | MCP |
|--|-----------------|-----|
| Definition | Inline in your API call | Defined in separate server |
| Reusability | One app only | Any MCP host can use the server |
| Discovery | Hardcoded in your code | Client discovers tools automatically |
| Transport | Direct function call | JSON-RPC over stdio/SSE |
| Deployment | Embedded in app | Separate process |
| Standardization | Provider-specific (OpenAI vs Claude format) | One standard protocol |

## How MCP Tools Appear to an LLM
From the LLM's perspective, there's no difference. MCP tools are passed to the LLM as function schemas — same format as regular tool calling. The MCP layer is transparent.

## When to Use Raw Tool Calling
- Simple, single-app use case
- No need for tool reuse across multiple apps
- Building a tightly coupled system

## When to Use MCP
- Building tools that should work across multiple AI apps
- Want a separate, maintainable server process
- Need the standardized ecosystem (existing MCP servers)
- Building enterprise tooling others will connect to

## Can They Be Combined?
Yes. An agent might use both: raw tool calls for app-specific functions + MCP tools from external servers. From the model's view, all tools look the same.
