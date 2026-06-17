# Upriver MCP — install guide for AI agents

This file tells an AI coding agent how to add the Upriver MCP server to a
client. Upriver is a **remote, hosted** server — there is nothing to clone,
build, install, or run locally. Configuration is a single entry pointing at the
hosted endpoint.

## Server facts

- **Name:** `upriver`
- **Endpoint:** `https://mcp.upriver.ai/mcp`
- **Transport:** Streamable HTTP (remote URL — not stdio, no command/args)
- **Auth:** OAuth (the user signs in with Upriver in their client), or an Upriver
  API key sent in the `X-API-Key` request header.

## Steps

1. Do **not** install any package or start a local process. This server is
   reached over HTTPS at the endpoint above.
2. Add the server to the client's MCP configuration using the remote-URL form:

   ```json
   {
     "mcpServers": {
       "upriver": {
         "url": "https://mcp.upriver.ai/mcp"
       }
     }
   }
   ```

   This generic form is correct for Cursor and most clients, but a few use a
   different shape — using the generic block with them silently fails:

   - **Claude Code (CLI):** prefer the command
     `claude mcp add --transport http upriver https://mcp.upriver.ai/mcp`
     (append `--header "X-API-Key: <KEY>"` for API-key auth).
   - **VS Code (GitHub Copilot):** top-level key is `servers` (not `mcpServers`)
     and a `"type": "http"` field is required.
   - **Cline:** add `"type": "streamableHttp"` to the server entry, or it falls
     back to legacy SSE and the connection fails.
   - **Windsurf:** the URL field is `serverUrl`, not `url`.

3. If — and only if — the client cannot perform OAuth and the user has supplied
   an Upriver API key, add it as a header instead of prompting for OAuth:

   ```json
   {
     "mcpServers": {
       "upriver": {
         "url": "https://mcp.upriver.ai/mcp",
         "headers": { "X-API-Key": "<USER_UPRIVER_API_KEY>" }
       }
     }
   }
   ```

   Never invent or hard-code a key. If no key is available and the client
   supports OAuth, rely on OAuth and let the user sign in.
4. After adding the config, the client should list two tools:
   `breakout_search_topics` and `breakout_list_topics`. Both are read-only and
   safe to auto-approve.

## Tools

- **`breakout_search_topics`** — search emerging "breakout" topics by keyword.
  Params: `query` (required, string), `vertical` (`tech` | `sports` |
  `politics`), `limit` (1–25, default 10), `citation_sources` (subset of
  `news` | `reddit` | `twitter`).
- **`breakout_list_topics`** — list breakout topics gaining traction now.
  Params: `vertical`, `status` (`active` default | `emerging` | `trending` |
  `declining` | `detected` | `all`), `limit`, `citation_sources`.

Each tool returns up to `limit` topics (max 25), each with up to 5 grounded
source citations and a `has_more` flag.

More of the Upriver API (creators, audiences, brands, products, sponsorships)
will be exposed as MCP tools over time; the full API is documented at
https://docs.upriver.ai. Re-list the server's tools to pick up new ones.

## Access

Access is provisioned per Upriver account. If tool calls return an
access/provisioning message, the user should request access at
https://dashboard.upriver.ai or email support@upriver.ai — this is expected for
accounts that are not yet enabled and is not a configuration error.
