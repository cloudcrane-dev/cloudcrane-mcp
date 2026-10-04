# CloudCrane MCP

[![smithery badge](https://smithery.ai/badge/cloudcrane/workspace)](https://smithery.ai/servers/cloudcrane/workspace)

Connect your AI agent to [CloudCrane](https://cloudcrane.ai) over MCP.

CloudCrane turns a messy catalog into data an agent can be trusted with. Rules
run before any model, the model may only answer from values you allowed, and
every value carries a receipt saying how it was decided. Safety exclusions are
enforced in the database query, so a record whose value is unknown is left out
rather than assumed safe.

There are two MCP endpoints. Both speak **Streamable HTTP** (stateless, no SSE stream).

| Endpoint | Signs in with | What it opens |
|---|---|---|
| `https://cloudcrane.ai/api/build/mcp` | Your CloudCrane account (OAuth), or a build key `cc_build_…` | Your workspace, for your own agent while you build |
| `https://cloudcrane.ai/api/mcp/<tool>` | A tool key `cc_live_…` | One deployed tool, for your end users' agents |

## The workspace MCP

Let your own agent read what you are building and, if you allow it, help build it.

### Connect with OAuth

In an MCP client that supports OAuth sign-in, add the URL with no key:

```
https://cloudcrane.ai/api/build/mcp
```

The client opens a CloudCrane page where a workspace owner picks the workspace
and what the agent may do, then signs you in. Or install it from
[Smithery](https://smithery.ai/servers/cloudcrane/workspace).

Tested with **Cursor**, **Cline** and **Smithery**. Exact settings for each are
in [`llms-install.md`](llms-install.md), which an agent can follow to set it up.

**Cursor** (`~/.cursor/mcp.json`):

```json
{ "mcpServers": { "cloudcrane": { "url": "https://cloudcrane.ai/api/build/mcp" } } }
```

**Cline** (`cline_mcp_settings.json`):

```json
{ "mcpServers": { "cloudcrane": { "transport": { "type": "streamableHttp", "url": "https://cloudcrane.ai/api/build/mcp" } } } }
```

The page shows where it will send you back before anything else, because an
app's name is only what it calls itself. It starts on read only. Each app you
approve shows up in the dashboard under **Developers**, where you can revoke it.

### Or with a build key

For a client without OAuth, an owner makes a build key under **Developers** and
sends it as a header:

```sh
claude mcp add --transport http cloudcrane https://cloudcrane.ai/api/build/mcp \
  --header "Authorization: Bearer $CLOUDCRANE_BUILD_KEY"
```

### What it can do

**Every connection reads:** `list_datasets`, `get_dataset`, `list_contracts`,
`get_readiness`, `list_review_items`, `get_receipts`, `list_value_sets`, `get_run`.
They run inside a read-only database transaction.

**A connection allowed to build also gets:** `create_dataset`, `create_field`,
`update_field`, `create_value_set`, `import_value_set_version`, `start_run`,
`publish_release`. Each goes through the same checks as the dashboard and is
recorded as made by that connection.

**What no connection can do:** publish past the accuracy gate, decide a review
item, edit a stored value, withhold a record, delete anything, or remove a value
from a safety field. Those stay with a person, because a receipt names who decided.

Imported record contents stay hidden unless the owner turns them on. The
workspace MCP comes with the Team plan.

Full reference: [cloudcrane.ai/docs/build-mcp](https://cloudcrane.ai/docs/build-mcp).

## A deployed tool

Each tool you deploy is its own MCP server. Your agent sees `search_<tool>` and
`get_<tool>`, plus `find_values` when the tool has value set fields. Their input
schema is generated from your contracts, so the agent picks values from an enum
of your list and cannot ask for one you never defined.

**Claude Code**

```sh
claude mcp add --transport http catalog https://cloudcrane.ai/api/mcp/catalog \
  --header "Authorization: Bearer $CLOUDCRANE_TOOL_KEY"
```

**Claude Desktop, Cursor, Windsurf**

```json
{
  "mcpServers": {
    "catalog": {
      "url": "https://cloudcrane.ai/api/mcp/catalog",
      "headers": { "Authorization": "Bearer cc_live_..." }
    }
  }
}
```

Some clients call the block `servers` instead of `mcpServers`, and some want `"type": "http"` next to the url.

- **n8n:** MCP Client Tool node, transport *HTTP Streamable*, Bearer authentication.
- **LangChain:** `MultiServerMCPClient` with transport `streamable_http` and a headers dict.
- **OpenAI Agents SDK:** `MCPServerStreamableHttp` with the url and headers.

The same tool also answers plain REST at `POST /api/v1/tools/<tool>/search`.
Full reference: [cloudcrane.ai/docs/deploy](https://cloudcrane.ai/docs/deploy).

## Things that look like a broken server

- **406:** MCP requires `Accept: application/json, text/event-stream` on every POST, even though these endpoints never send a stream.
- **405 on GET:** the endpoints are stateless and offer no SSE stream, so only POST is allowed. A client that silently falls back to SSE connects but lists no tools.
- **403 on the workspace MCP:** it opens a whole workspace, so a request from a browser (any request with an `Origin` header) is refused. Call it from a server or a desktop client.
- **402 on the workspace MCP:** the workspace isn't on a plan that includes it.

## Links

- [Docs](https://cloudcrane.ai/docs)
- [Playground](https://cloudcrane.ai/playground): the pipeline in your browser, no sign-up
- [Integrations](https://cloudcrane.ai/integrations)

This repository holds documentation and the registry entry (`server.json`), not
the server's source.
