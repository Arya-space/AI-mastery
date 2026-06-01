# What Is MCP

**MCP (Model Context Protocol)** is an open standard developed by Anthropic that defines how AI applications connect to external tools, data sources, and services.

## One-Line Definition
MCP is a standardized protocol so that any AI app (host) can connect to any external capability (server) without custom integration code.

## The Problem It Solves
Without MCP: every AI app needs custom code to integrate each tool.
With MCP: build one server, any MCP-compatible app can use it.

## Key Concepts
- **Host**: the AI application (Claude Desktop, an IDE, your custom app)
- **Client**: MCP protocol handler embedded in the host
- **Server**: standalone process exposing tools/resources/prompts
- **Tools**: callable functions (like tool calling)
- **Resources**: data sources (files, DB rows, URLs)
- **Prompts**: reusable prompt templates

## Who Made It / Status
- Created by Anthropic, released late 2024
- Open standard, MIT licensed
- Adopted by OpenAI, Google, and major tool vendors (GitHub, Slack, etc.)
- Supported natively in Claude Desktop and Claude Code

## Interview Answer
"MCP is a standard protocol that lets AI apps connect to external tools and data. Instead of each AI app building custom integrations for each tool, MCP separates the concerns: apps implement one MCP client, tools implement one MCP server. They interoperate automatically."
