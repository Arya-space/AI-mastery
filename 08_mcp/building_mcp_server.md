# Building an MCP Server

## Setup
```bash
pip install mcp
```

## Minimal MCP Server (Python)
```python
# server.py
from mcp.server import Server
from mcp.server.stdio import stdio_server
from mcp import types

app = Server("my-server")

@app.list_tools()
async def list_tools() -> list[types.Tool]:
    return [
        types.Tool(
            name="get_weather",
            description="Get current weather for a city",
            inputSchema={
                "type": "object",
                "properties": {
                    "location": {"type": "string", "description": "City name"}
                },
                "required": ["location"]
            }
        )
    ]

@app.call_tool()
async def call_tool(name: str, arguments: dict) -> list[types.TextContent]:
    if name == "get_weather":
        location = arguments["location"]
        # call weather API...
        return [types.TextContent(type="text", text=f"25°C, sunny in {location}")]
    raise ValueError(f"Unknown tool: {name}")

if __name__ == "__main__":
    import asyncio
    asyncio.run(stdio_server(app))
```

## Run Locally
```bash
python server.py
```

## Connect to Claude Desktop
Add to `~/.config/claude/claude_desktop_config.json`:
```json
{
  "mcpServers": {
    "weather": {
      "command": "python",
      "args": ["/path/to/server.py"]
    }
  }
}
```

## Adding Resources
```python
@app.list_resources()
async def list_resources() -> list[types.Resource]:
    return [types.Resource(uri="file:///data/report.txt", name="Report", mimeType="text/plain")]

@app.read_resource()
async def read_resource(uri: str) -> str:
    with open("/data/report.txt") as f:
        return f.read()
```

## Testing
Use the MCP Inspector (official CLI tool) to test your server without a full host:
```bash
npx @modelcontextprotocol/inspector python server.py
```
