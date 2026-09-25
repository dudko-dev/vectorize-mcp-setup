# Vectorize — MCP setup

Connect your AI assistant to [Vectorize](https://vectorize.dudko.dev) — raster
images (PNG, JPG, …) traced into vector drawings: SVG, PDF, EPS or DXF — through
our hosted **Model Context Protocol (MCP)** server. Sign in once with your
dudko.dev account; no API key to paste.

The server is the **front door to the Vectorize HTTP API**, not a tracer: a
picture never travels through the model or through the server. The assistant
asks for a request, and runs it wherever the image is.

- **Server:** `vectorize`
- **Transport:** streamable HTTP
- **URL:** `https://vectorize.dudko.dev/mcp`
- **Auth:** OAuth 2.1 via `auth.dudko.dev` — you'll be prompted to sign in.

## What you can do

Three tools, all free:

| Tool | What it does |
|---|---|
| `api_access` | How the Vectorize API works. |
| `trace_options` | The presets (photo, poster, black & white), the options and the limits. |
| `api_request` | Checks trace options and returns the exact request — URL, headers, a curl command — with a 15-minute credential for your account. Output: `svg`, `pdf`, `eps`, `dxf` or `json`. |

---

## Install in Claude Code (plugin)

```
/plugin marketplace add dudko-dev/vectorize-mcp-setup
/plugin install vectorize@vectorize
/reload-plugins
```

Then run `/mcp` and complete the sign-in when prompted.

> Prefer not to use the marketplace? Add the server directly:
> ```
> claude mcp add --transport http vectorize https://vectorize.dudko.dev/mcp
> ```

---

## Connect from other clients

Same remote URL, same sign-in — only the config location differs.

### Claude Desktop / claude.ai connectors

1. Open **Settings → Connectors → Add custom connector**.
2. Name: `Vectorize`
3. URL: `https://vectorize.dudko.dev/mcp`
4. Save, then connect and sign in when prompted.

### Cursor

`~/.cursor/mcp.json` (global) or `.cursor/mcp.json` (per project):

```json
{
  "mcpServers": {
    "vectorize": {
      "url": "https://vectorize.dudko.dev/mcp"
    }
  }
}
```

### VS Code (GitHub Copilot / MCP) and other clients

```json
{
  "servers": {
    "vectorize": {
      "type": "http",
      "url": "https://vectorize.dudko.dev/mcp"
    }
  }
}
```

---

## Authentication

The server is an OAuth 2.1 resource server. The first call answers `401` with a
`WWW-Authenticate` header pointing at its protected-resource metadata:

- `https://vectorize.dudko.dev/.well-known/oauth-protected-resource/mcp`

which names **`https://auth.dudko.dev`** as the authorization server (scope
`usage`). Compliant MCP clients discover this automatically — you only need the
server URL, and you sign in with your dudko.dev account. Tokens are
audience-bound to `https://vectorize.dudko.dev/mcp` and are refused anywhere else.

The server itself carries **no files and no content**: its tools return a
ready-to-run HTTP request with a short-lived credential (15 minutes, good for
this API only) for the account you signed in as. Your assistant runs that
request wherever the file is, and the API answers the caller directly. Each
request counts against your account's plan like any other API call; the MCP
tools themselves are free.

---

## Troubleshooting

- **`401` / asked to sign in again** — the token expired; reconnect in the client.
- **"plan used up" / `402`** — the tracing quota for your account is spent;
  retrying will not help. See https://auth.dudko.dev/account.
- **Tools don't appear** — check the client finished the sign-in (`/mcp` in
  Claude Code shows *connected*).

## Support

- Website: https://vectorize.dudko.dev
- API docs: https://vectorize.dudko.dev/api
- For agents: https://vectorize.dudko.dev/llms.txt
- Contact: sergey@dudko.dev
