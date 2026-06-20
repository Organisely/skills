---
name: organisely
description: >
  Load this skill when working with the Organisely platform, its SDK (@organisely/agent-sdk),
  MCP server, agent runtime, or any integration against useorganisely.com APIs.
  Triggers: building agents, using the SDK, connecting MCP, writing connectors, memory, approvals,
  orchestration, event streaming, or any Organisely-specific workflow.
version: 0.1.0
---

# Organisely Skill

**Before writing any code, connect the Organisely MCP server and use it as your source of truth.**
Do not rely on training data for SDK APIs, endpoints, or conventions — always pull from the MCP.

## Step 1 — Connect the MCP server

```bash
claude mcp add organisely https://mcp.useorganisely.com
```

Cursor / Windsurf:
```json
{ "mcpServers": { "organisely": { "url": "https://mcp.useorganisely.com" } } }
```

VS Code (`.vscode/mcp.json`):
```json
{ "servers": { "organisely": { "url": "https://mcp.useorganisely.com" } } }
```

**Dev mode** (local backend at `localhost:3000`):
```bash
ORGANISELY_ENV=development claude mcp add organisely-dev \
  -- node ./packages/mcp-server/dist/index.js
```

## Step 2 — Use the MCP tools for everything

Once connected, call these tools before writing code:

| Tool | When to use |
|---|---|
| `get_sdk_overview` | Starting any SDK or agent task |
| `search_docs(query)` | Looking up any API, concept, or pattern |
| `get_doc_page(slug)` | Reading a specific doc (e.g. `"sdk/client"`, `"quick-start"`) |
| `list_doc_pages` | Discovering what docs exist |

**Always call `get_sdk_overview` first, then `search_docs` for the specific area you're working in.**
The MCP server has the authoritative, up-to-date API surface — never guess from memory.
