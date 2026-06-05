# Client configurations

Drop-in config snippets for popular MCP clients. Replace `YOUR_POEDITOR_API_TOKEN` with a token from [poeditor.com/account/api](https://poeditor.com/account/api).

| Client | Config file | Config location |
|---|---|---|
| Claude Code | [claude_code.md](./claude_code.md) | CLI command — OAuth supported natively |
| Claude Desktop | [claude_desktop_config.json](./claude_desktop_config.json) | macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`<br>Windows: `%APPDATA%\Claude\claude_desktop_config.json` |
| Cursor | [cursor_config.json](./cursor_config.json) | `~/.cursor/mcp.json` |
| Windsurf | [windsurf_config.json](./windsurf_config.json) | `~/.codeium/windsurf/mcp_config.json` |
| VS Code (Copilot) | [vscode_copilot.json](./vscode_copilot.json) | `.vscode/mcp.json` |
| VS Code (Cline) | [cline_config.json](./cline_config.json) | VS Code settings → Cline MCP Servers |
| VS Code (Continue) | [continue_config.json](./continue_config.json) | `~/.continue/config.json` |
| VS Code (MCP ext.) | [vscode_settings.json](./vscode_settings.json) | `.vscode/settings.json` or user settings |
| Zed | [zed_settings.json](./zed_settings.json) | `~/.config/zed/settings.json` |
| Amazon Q Developer | [amazon_q.json](./amazon_q.json) | `~/.aws/amazonq/mcp.json` |
| Gemini CLI | [gemini_cli.json](./gemini_cli.json) | `~/.gemini/settings.json` |
| Augment | [augment.json](./augment.json) | VS Code settings → Augment MCP Servers |

After editing, **restart the client** for the server to load.

## Verifying

Once connected, ask your assistant:

> List my POEditor projects

You should get a list of your projects with IDs, names, and last-updated timestamps.

## Troubleshooting

- **401 Unauthorized** — token is wrong or expired. Regenerate at [poeditor.com/account/api](https://poeditor.com/account/api).
- **Connection refused** — check your firewall allows outbound HTTPS to `mcp.poeditor.com`.
- **Tools not appearing** — confirm the client supports remote MCP (HTTP transport). Older clients may only support stdio.
