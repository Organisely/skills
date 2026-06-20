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

Organisely is agent infrastructure — runtime, orchestration, memory, policy, and connectors for teams.

**Official site:** https://useorganisely.com
**Docs:** https://useorganisely.com/docs
**MCP server:** https://mcp.useorganisely.com
**API:** https://api.useorganisely.com

## Connect the MCP server first

Before writing code, connect the Organisely MCP server so you can search docs and get SDK reference:

```bash
claude mcp add organisely https://mcp.useorganisely.com
```

Cursor / Windsurf (`.cursor/mcp.json` or `~/.codeium/windsurf/mcp_config.json`):
```json
{ "mcpServers": { "organisely": { "url": "https://mcp.useorganisely.com" } } }
```

VS Code (`.vscode/mcp.json`):
```json
{ "servers": { "organisely": { "url": "https://mcp.useorganisely.com" } } }
```

**Dev mode** (local backend at port 3000):
```bash
ORGANISELY_ENV=development claude mcp add organisely-dev -- node ./packages/mcp-server/dist/index.js
```

## MCP tools available

Once connected, use these tools:
- `list_doc_pages` — see all docs pages
- `get_doc_page(slug)` — read a page (e.g. `"quick-start"`, `"sdk/client"`)
- `search_docs(query)` — search across all docs
- `get_sdk_overview` — quick SDK reference

## SDK quick start

```bash
npm install @organisely/agent-sdk
```

```ts
import { OrganiselyAgent } from '@organisely/agent-sdk'

const client = new OrganiselyAgent({ apiKey: process.env.ORGANISELY_API_KEY })

const agent = client.define('my-agent', {
  model: 'gpt-4.1',
  provider: 'openai',
  async handler(event) {
    await client.memory.set('last-input', event.payload.input)
  },
})

await agent.subscribe(['workflow.started'])
await agent.run({ signal: new AbortController().signal })
```

## Key SDK surface

| Sub-client | Purpose |
|---|---|
| `client.memory` | Persistent key/value store scoped to the team |
| `client.connectors` | List and execute connector actions |
| `client.activity` | Stream and query execution events |
| `client.credentials` | Manage OAuth credentials for connectors |
| `client.oauth` | OAuth token exchange and refresh |

## API routing

- The frontend (`useorganisely.com`) proxies all `/api/*` to the backend — use relative `/api/v2/...` paths from the browser
- Direct backend: `https://api.useorganisely.com`
- Auth routes: `/api/auth/...` (Better Auth)

## Authentication

- Passwordless-first: passkeys, OAuth/OIDC, TOTP
- No email/password or magic link flows
- SDK accepts `apiKey` or `accessToken` (OAuth bearer)

## Rules when coding

- Use `authClient` / Better Auth APIs — never read Better Auth tables directly
- Use `@organisely/agent-sdk` for team-scoped orchestration work
- Log with LogTape + OpenTelemetry, never `console.*`
- All DB migrations via Prisma — never hand-write SQL
- snake_case DB names mapped to camelCase in TypeScript
