# Deployment

The POEditor MCP server is a **remote MCP server** operated by POEditor. There is nothing to install or deploy on your side — connect your MCP client directly to the endpoint.

## Endpoint

```
https://mcp.poeditor.com/mcp
```

- Transport: Streamable HTTP (MCP spec compliant)
- TLS 1.3 terminated by POEditor
- Auth: Bearer token or OAuth 2.1 with PKCE

## Client configuration

Add the endpoint to your MCP client config:

```json
{
  "mcpServers": {
    "poeditor": {
      "url": "https://mcp.poeditor.com/mcp",
      "headers": { "Authorization": "Bearer YOUR_TOKEN" }
    }
  }
}
```

See [`../examples/`](../examples) for client-specific configs (Claude Desktop, Cursor, Windsurf, Cline, VS Code, Continue).

## Custom deployments

For air-gapped environments, regulated industries, or other custom-deployment requirements, contact [info@poeditor.com](mailto:info@poeditor.com).
