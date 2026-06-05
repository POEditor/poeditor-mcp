# Claude Code

## Option 1: OAuth (recommended)

No token needed. Claude Code handles the browser login and token storage automatically.

```bash
claude mcp add poeditor --transport http https://mcp.poeditor.com/mcp
```

On first use, a browser window opens to POEditor for login and access approval. After that, the connection is automatic on every session.

## Option 2: API token

Generate a token at [poeditor.com/account/api](https://poeditor.com/account/api), then:

```bash
claude mcp add poeditor --transport http https://mcp.poeditor.com/mcp \
  --header "Authorization: Bearer YOUR_POEDITOR_API_TOKEN"
```

## Verifying

```bash
claude mcp list
```

You should see `poeditor` listed as connected. Then ask Claude:

> List my POEditor projects
