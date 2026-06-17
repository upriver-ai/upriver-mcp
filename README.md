<p align="center">
  <img src="https://mcp.upriver.ai/icon.png" width="96" height="96" alt="Upriver" />
</p>

<h1 align="center">Upriver MCP</h1>

<p align="center"><em>Real-time context on creators, audiences, brands, trends, and sponsorships — for AI applications.</em></p>

## About Upriver

[Upriver](https://upriver.ai) provides an API that turns what's happening online
into real-time, reliable context for AI applications. It delivers
evidence-backed signals — each grounded in real sources — across:

- **Creators** — relevant creators and profiles
- **Audiences** — personas, interests, language, and humor
- **Brands** — brands and how they're being discussed
- **Products** — product mentions and use cases
- **Trends** — topics and formats gaining attention
- **Sponsorships** — brand–creator relationships and placements

This repository is the home of Upriver's **Model Context Protocol (MCP)**
server, which brings that context to any MCP-compatible AI assistant or agent.

## Connect

| | |
|---|---|
| **Endpoint** | `https://mcp.upriver.ai/mcp` |
| **Transport** | Streamable HTTP |
| **Auth** | Sign in with Upriver (OAuth), or send your Upriver API key in the `X-API-Key` header |

It's a remote, hosted server — nothing to install or run. **OAuth sign-in works in
every client.** The `X-API-Key` header is supported by Claude Code, Cursor, VS Code,
Cline, Windsurf, Codex CLI, Goose, and Zed — but **not** by Claude's or ChatGPT's
connector UIs, which are OAuth-only.

### Claude Code (CLI)

```bash
claude mcp add --transport http upriver https://mcp.upriver.ai/mcp
```

To use an API key instead of OAuth, pass it as a header:

```bash
claude mcp add --transport http upriver https://mcp.upriver.ai/mcp \
  --header "X-API-Key: YOUR_UPRIVER_API_KEY"
```

### Cursor

Add to `~/.cursor/mcp.json` (global) or `.cursor/mcp.json` (per-project), then connect
it in **Settings → Tools & Integrations** to sign in:

```json
{ "mcpServers": { "upriver": { "url": "https://mcp.upriver.ai/mcp" } } }
```

For API-key auth, add `"headers": { "X-API-Key": "YOUR_UPRIVER_API_KEY" }`.

### VS Code (GitHub Copilot)

VS Code uses a different shape — top-level **`servers`** and a required **`"type": "http"`**.
Add to `.vscode/mcp.json` (or run **MCP: Add Server**):

```json
{ "servers": { "upriver": { "type": "http", "url": "https://mcp.upriver.ai/mcp" } } }
```

For API-key auth, add `"headers": { "X-API-Key": "YOUR_UPRIVER_API_KEY" }`.

### Claude Desktop & claude.ai

In Claude, go to **Customize → Connectors → "+" → Add custom connector**, name it
`Upriver`, paste `https://mcp.upriver.ai/mcp`, then **Connect** and sign in.
(Claude's connector is OAuth-only — there's no field for an API key, and don't add a
raw `url` entry to `claude_desktop_config.json`; the desktop app rejects it.)

### Cline

Use the **Remote Servers** tab (Transport: **Streamable HTTP**), or edit
`cline_mcp_settings.json` — the `type` field is required, or Cline falls back to legacy
SSE and fails:

```json
{ "mcpServers": { "upriver": { "type": "streamableHttp", "url": "https://mcp.upriver.ai/mcp" } } }
```

### Windsurf

Edit `~/.codeium/windsurf/mcp_config.json` — Windsurf's field is **`serverUrl`**:

```json
{ "mcpServers": { "upriver": { "serverUrl": "https://mcp.upriver.ai/mcp" } } }
```

<details>
<summary><b>More clients</b> — Codex CLI, ChatGPT, Goose, Zed</summary>

**Codex CLI (OpenAI)**

```bash
codex mcp add upriver --url https://mcp.upriver.ai/mcp --header X-API-Key=YOUR_UPRIVER_API_KEY
```

**ChatGPT** — Enable **Developer mode** (Settings → Apps & Connectors → Advanced), then
**Settings → Connectors → Create**, name it `Upriver`, paste `https://mcp.upriver.ai/mcp`,
choose **OAuth**, and sign in. (No custom-header field — OAuth only in the UI.)

**Goose** — add to `~/.config/goose/config.yaml`:

```yaml
extensions:
  upriver:
    type: streamable_http
    uri: https://mcp.upriver.ai/mcp
    enabled: true
    headers:
      X-API-Key: "${UPRIVER_API_KEY}"
```

**Zed** — add to `settings.json` under `context_servers`:

```json
{ "context_servers": { "upriver": { "url": "https://mcp.upriver.ai/mcp", "headers": { "X-API-Key": "YOUR_UPRIVER_API_KEY" } } } }
```

</details>

### Any other MCP client

Most clients use the Cursor shape above (`mcpServers` + `url`, optional `headers`).
For agents that read install manifests, see [`llms-install.md`](./llms-install.md).

> **JetBrains IDEs:** MCP works, but the IDE currently drops custom request headers and
> doesn't yet support OAuth for remote servers, so Upriver's `X-API-Key` auth can't
> connect there yet — we'll list it once JetBrains ships the fix.

## Tools available today

The MCP server currently exposes Upriver's **breakout topics** — emerging
subjects gaining traction online across tech, sports, and politics, each
returned with grounded source citations so the model reasons from real evidence
instead of guessing.

Both tools are read-only. Each returns up to `limit` topics (default 10,
max 25), every topic carrying up to 5 source citations, plus a `has_more` flag
when more topics exist beyond the page.

### `breakout_search_topics`

Search breakout topics by keyword.

| Parameter | Type | Description |
|---|---|---|
| `query` | string · **required** | Natural-language search, e.g. `"AI video effects"`. |
| `vertical` | enum | Restrict to `tech`, `sports`, or `politics`. |
| `limit` | integer | 1–25 (default 10). |
| `citation_sources` | array | Restrict citations to any of `news`, `reddit`, `twitter`. |

### `breakout_list_topics`

Browse breakout topics gaining traction right now.

| Parameter | Type | Description |
|---|---|---|
| `vertical` | enum | Restrict to `tech`, `sports`, or `politics`. |
| `status` | enum | Lifecycle filter: `active` (default), `emerging`, `trending`, `declining`, `detected`, or `all`. |
| `limit` | integer | 1–25 (default 10). |
| `citation_sources` | array | Restrict citations to any of `news`, `reddit`, `twitter`. |

Each topic includes its name, vertical, lifecycle status, a momentum signal, and
its top citations (title, URL, source category, and publish time).

## More on the way

Breakout topics are the first slice of the Upriver API to land as MCP tools. The
full API — Creators, Audiences, Brands, Products, Trends, and Sponsorships — is
documented at **[docs.upriver.ai](https://docs.upriver.ai)**, and we're rolling
those endpoints out as MCP tools. Watch this repository for new tools as they
ship.

## Access

Upriver MCP access is provisioned per account. Request access at
[dashboard.upriver.ai](https://dashboard.upriver.ai) or email
[support@upriver.ai](mailto:support@upriver.ai). Once your account is enabled,
the same Upriver sign-in (or API key) works across every MCP client.

## Links

- **Docs** — https://docs.upriver.ai
- **Dashboard** — https://dashboard.upriver.ai
- **Website** — https://upriver.ai

## License

The contents of this repository — connection documentation and listing
metadata — are released under the [MIT License](./LICENSE).

The hosted Upriver MCP service and the Upriver API are operated by Potato
Laboratories, Inc. d/b/a Upriver and are governed by Upriver's
[Terms of Service](https://upriver.ai/terms). The MIT License covers this
repository only — not the service, nor the data it returns. "Upriver" and the
Upriver logo are trademarks of Potato Laboratories, Inc. and are not covered by
the MIT License.
