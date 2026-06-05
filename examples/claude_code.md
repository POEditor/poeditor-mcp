# Claude Code

Claude Code supports OAuth — no token needed.

```bash
claude mcp add poeditor-production --transport http https://mcp.poeditor.com/mcp
```

On first use, Claude Code opens a browser for POEditor login and stores the token automatically.

To use a token instead:

```bash
claude mcp add poeditor --transport http https://mcp.poeditor.com/mcp \
  --header "Authorization: Bearer YOUR_POEDITOR_API_TOKEN"
```
