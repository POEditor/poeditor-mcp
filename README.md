# POEditor MCP Server

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![MCP](https://img.shields.io/badge/MCP-compatible-blue)](https://modelcontextprotocol.io)
[![POEditor](https://img.shields.io/badge/POEditor-Official-orange)](https://poeditor.com)

**Official Model Context Protocol (MCP) server for [POEditor](https://poeditor.com)** — the translation management platform. Connect any MCP-compatible AI assistant directly to your POEditor projects.

> 37 tools across the full POEditor v2 API. Manage projects, terms, translations, contributors, and languages. Auto-translate with Google, Microsoft, or DeepL. Export to any format your client supports. OAuth 2.1 with PKCE.

---

## Endpoint

```text
https://mcp.poeditor.com/mcp
```

Remote MCP server, hosted by POEditor. No installation required. Streamable HTTP transport, MCP spec compliant.

---

## Quick start

### 1. Get your API token

Generate a token at [poeditor.com/account/api](https://poeditor.com/account/api).

### 2. Add to your client

**Claude Code** — OAuth or API token:

```bash
claude mcp add poeditor --transport http https://mcp.poeditor.com/mcp
```

On first use Claude Code opens a browser for POEditor login and stores the token automatically. To use an API token instead:

```bash
claude mcp add poeditor --transport http https://mcp.poeditor.com/mcp \
  --header "Authorization: Bearer YOUR_POEDITOR_API_TOKEN"
```

**Claude Desktop** — edit `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "poeditor": {
      "url": "https://mcp.poeditor.com/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_POEDITOR_API_TOKEN"
      }
    }
  }
}
```

**Cursor** — edit `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "poeditor": {
      "url": "https://mcp.poeditor.com/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_POEDITOR_API_TOKEN"
      }
    }
  }
}
```

**Windsurf** — edit `~/.codeium/windsurf/mcp_config.json`:

```json
{
  "mcpServers": {
    "poeditor": {
      "serverUrl": "https://mcp.poeditor.com/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_POEDITOR_API_TOKEN"
      }
    }
  }
}
```

**VS Code (GitHub Copilot)** — add to `.vscode/mcp.json`:

```json
{
  "servers": {
    "poeditor": {
      "url": "https://mcp.poeditor.com/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_POEDITOR_API_TOKEN"
      }
    }
  }
}
```

**Zed** — add to `~/.config/zed/settings.json`:

```json
{
  "context_servers": {
    "poeditor": {
      "command": {
        "path": "npx",
        "args": [
          "-y", "mcp-remote",
          "https://mcp.poeditor.com/mcp",
          "--header", "Authorization: Bearer YOUR_POEDITOR_API_TOKEN"
        ]
      },
      "settings": {}
    }
  }
}
```

**Gemini CLI** — add to `~/.gemini/settings.json`:

```json
{
  "mcpServers": {
    "poeditor": {
      "url": "https://mcp.poeditor.com/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_POEDITOR_API_TOKEN"
      }
    }
  }
}
```

**Amazon Q Developer** — add to `~/.aws/amazonq/mcp.json`:

```json
{
  "mcpServers": {
    "poeditor": {
      "url": "https://mcp.poeditor.com/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_POEDITOR_API_TOKEN"
      }
    }
  }
}
```

See [`examples/`](./examples) for Cline, Continue, Augment, and other client configurations.

### 3. Try it

Ask your assistant:

```text
List all my POEditor projects
```

---

## Tools

> The live tool list is served by the MCP endpoint via `tools/list`. Categories below reflect current coverage.

| Category | Tools |
|---|---|
| **Projects** | list, details, add, update, delete, sync, upload, export |
| **Languages** | available languages, list project languages, add, update, delete |
| **Terms** | list, add, update, delete, details, comment |
| **Translations** | commit, propose, update, delete, fill from TM, list fuzzy, list pending proofread, list untranslated, mark proofread |
| **Progress** | translation status, proofread progress |
| **Quality** | QA checks, AI quality evaluation (MQM scoring) |
| **Contributors** | list, add, remove |
| **Automation** | automatic translation (Google, Microsoft, DeepL) |
| **Account** | account info |

Full schema: see [`docs/tools.md`](./docs/tools.md).

---

## Authentication

- **Token** — POEditor API token, passed as `Authorization: Bearer <token>` header
- **OAuth 2.1 (PKCE)** — supported for one-click connect in Claude Code, Claude.ai Connectors, and compatible clients

Token scope: user's own projects only. No cross-tenant access. See [Authentication](./docs/authentication.md).

---

## Export formats

`export_strings_file` returns a download URL for the generated file. Any format POEditor supports can be requested — the LLM can read, parse, and work with the content directly regardless of format. See [`docs/tools.md`](./docs/tools.md) for the full format list.

---

## Examples

```text
List all my POEditor projects
Show me untranslated terms in project 123456 for Spanish
Add the term "checkout_button" with English value "Check out" to project 123456
Export Spanish translations from project 123456 as JSON
Auto-translate untranslated French terms in project 123456 using DeepL
Show translation progress for all languages in project 123456
List all contributors for project 123456
```

---

## Supported clients

Anything that speaks MCP. Works with:

- Claude Code · Claude Desktop · Claude.ai Connectors
- Cursor · Windsurf · Zed
- VS Code (GitHub Copilot · Cline · Continue · MCP extension)
- Gemini CLI · Amazon Q Developer · Augment
- mcp-inspector

---

## Features

- 37 tools — full POEditor v2 API coverage plus tools beyond the raw API
- Export to any format POEditor supports
- Automatic translation via Google, Microsoft, or DeepL
- OAuth 2.1 with PKCE
- Streamable HTTP transport (MCP spec compliant)
- ISO 27001 certified infrastructure

---

## Links

- POEditor: [poeditor.com](https://poeditor.com)
- API docs: [poeditor.com/docs/api](https://poeditor.com/docs/api)
- MCP spec: [modelcontextprotocol.io](https://modelcontextprotocol.io)
- Support: [info@poeditor.com](mailto:info@poeditor.com)

## License

MIT — see [LICENSE](./LICENSE).
