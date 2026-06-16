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

It's a remote, hosted server — there's nothing to install or run.

### Claude (Desktop / claude.ai)

Settings → Connectors → **Add custom connector**, paste
`https://mcp.upriver.ai/mcp`, then sign in with Upriver when prompted.

### Cursor, VS Code, and other MCP clients

Add Upriver to your client's MCP configuration:

```json
{
  "mcpServers": {
    "upriver": {
      "url": "https://mcp.upriver.ai/mcp"
    }
  }
}
```

If your client authenticates with an API key instead of OAuth, pass it as a
header:

```json
{
  "mcpServers": {
    "upriver": {
      "url": "https://mcp.upriver.ai/mcp",
      "headers": { "X-API-Key": "YOUR_UPRIVER_API_KEY" }
    }
  }
}
```

For AI coding agents that read install manifests, see
[`llms-install.md`](./llms-install.md).

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
