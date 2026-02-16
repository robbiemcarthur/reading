# What is MCP (Model Context Protocol)

#ai #mcp #phase3

## Overview

MCP stands for **Model Context Protocol**. It's a standardised interface between AI models and external tools or data sources — think of it like REST for AI agents.

Before MCP, every integration between an AI model and an external system (a database, a note-taking app, a code repository) needed its own bespoke implementation. MCP creates a universal contract, similar to how USB standardised hardware connections or how REST standardised web APIs.

## Architecture

The protocol has two sides:

**MCP Server** — A lightweight process that exposes a set of capabilities (called "tools") over the protocol. For example, the `mcp-obsidian` server you set up exposes tools like "read a file", "search notes", and "append content". Servers run locally on your machine or on a remote host.

**MCP Client** — The AI application that discovers available tools and calls them when needed. Claude Desktop is an MCP client. When you ask Claude about your notes, it recognises it needs vault access, calls the appropriate tool on the MCP server, receives the results, and incorporates them into its response.

## How It Works (Your Setup as an Example)

```
You ask Claude: "Find my notes about Kafka"
        ↓
Claude Desktop (MCP Client) recognises it needs vault access
        ↓
Calls the `search` tool on mcp-obsidian server
        ↓
mcp-obsidian queries Obsidian's REST API
        ↓
Results returned to Claude
        ↓
Claude incorporates them into its response
```

## Why This Matters for Your Career

MCP is rapidly becoming the standard for how AI agents interact with the world. Anthropic open-sourced the spec and it's being adopted across the industry. Understanding MCP is the equivalent of learning REST in the early 2010s:

- **Building MCP servers** — Exposing your team's tools and data to AI agents
- **Designing tool interfaces** — What capabilities to expose, how to structure them
- **Security & governance** — Controlling what agents can access and do
- **Multi-agent orchestration** — Agents using MCP to coordinate with each other and external systems

## Key Concepts

**Tools** — Functions that a server exposes (e.g., `search`, `read_file`, `create_note`). Each tool has a defined schema for inputs and outputs.

**Resources** — Data that a server can provide to an AI model for context (e.g., file contents, database records).

**Transports** — How clients and servers communicate. Common options: stdio (local processes), HTTP/SSE (network), WebSocket.

**Discovery** — How clients find available servers and their capabilities. Claude Desktop reads from a config file; Claude Code can auto-discover local servers.

## Practical Next Steps

1. You've already set up your first MCP integration (Obsidian + Claude Desktop)
2. Explore other MCP servers: GitHub, Slack, databases, file systems
3. Try building a simple MCP server yourself (great learning project for Week 14-15)
4. Read the MCP specification: https://modelcontextprotocol.io

## Related Notes
- [[AI Agentic Index]]
- [[Agent Architectures]]
- [[Working with AI Coding Agents]]
