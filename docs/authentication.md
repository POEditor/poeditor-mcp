# Authentication

## API token

1. Go to [poeditor.com/account/api](https://poeditor.com/account/api).
2. Click **Generate token** (or copy an existing one).
3. Pass it on every request as:

   ```
   Authorization: Bearer <token>
   ```

### Scope

- Token grants access to **all projects owned by or shared with the user**.
- No project-level token scoping (POEditor API limitation).
- Read + write permissions follow the user's project role.

### Rotation

Tokens don't expire. Rotate manually if compromised — old token can be revoked from the API access page.

### Rate limits

- Standard POEditor API limits apply: **60 requests/minute** per token.
- MCP server batches where possible. Heavy operations (full project export, bulk imports) count as one request.

---

## OAuth 2.1

OAuth 2.1 with PKCE is supported and is the recommended auth method for MCP clients that implement it. Used by:

- Claude Code (automatic browser flow)
- Claude.ai Connectors
- Any client that supports MCP OAuth

### Flow

```
1. Client opens https://poeditor.com/oauth/authorize?...
2. User logs in to POEditor and grants access
3. Redirect with auth code → client exchanges for access token
4. Token stored by the client; refreshed automatically
```

### Scopes

The server requests `read write` scope, granting full access to the authenticated user's projects.

| Scope | Grants |
|---|---|
| `read` | List and view projects, terms, translations, contributors |
| `write` | Create, update, and delete projects, terms, translations, contributors |

---

## Security

- HTTPS only (TLS 1.3)
- Tokens are never logged
- Server is ISO 27001 certified
- Bearer tokens transit in `Authorization` header — never in URL query strings
