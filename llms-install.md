# Installing the CloudCrane MCP server (for AI agents)

This is a **remote** MCP server. There is nothing to download, build or run:
add one entry to the client's MCP settings and sign in.

- **URL:** `https://cloudcrane.ai/api/apps/mcp`
- **Transport:** Streamable HTTP
- **Authentication:** OAuth, with dynamic client registration. Do **not** ask the user for an API key and do **not** add an `Authorization` header.

## Cline

Add this to `cline_mcp_settings.json` under `mcpServers`:

```json
{
  "mcpServers": {
    "cloudcrane": {
      "transport": {
        "type": "streamableHttp",
        "url": "https://cloudcrane.ai/api/apps/mcp"
      }
    }
  }
}
```

Then enable the server. Cline opens the user's browser on a CloudCrane page.

## Cursor

Add this to `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "cloudcrane": { "url": "https://cloudcrane.ai/api/apps/mcp" }
  }
}
```

## Claude Code

Run:

```
claude mcp add --transport http cloudcrane https://cloudcrane.ai/api/apps/mcp
```

Then the user types `/mcp` in Claude Code and picks `cloudcrane` to sign in.

## Antigravity (app, IDE and the `agy` CLI)

Add this to `~/.gemini/config/mcp_config.json` (or `<workspace>/.agents/mcp_config.json` for one project):

```json
{
  "mcpServers": {
    "cloudcrane": { "serverUrl": "https://cloudcrane.ai/api/apps/mcp" }
  }
}
```

Do **not** add an `oauth` block: Antigravity registers itself (dynamic client registration). Start a new session so it connects; in the app, the user
taps **Authenticate** next to it under Customizations → Installed → MCP Servers.

## What the user does in the browser

1. Signs in to CloudCrane (Google or an emailed code), if not already signed in. Signing up is free at https://cloudcrane.ai.
2. Picks the organisation. The page says what the connection will open there: an owner's opens its apps and its rooms; any other member's opens its rooms alone.
3. Clicks **Connect**. An owner's agent can then see, make, change, roll back and delete their apps, and use the organisation's rooms as them.

## Checking it worked

For an owner, the server should show as connected with **32 tools** and 4 prompts.

- 23 for apps: `list_apps`, `get_app`, `get_problems`, `get_blueprint`, `list_versions`, `list_remixes`, `get_remix`, `get_design_guide`, `check_publish`, `look_at_app`, `diff_versions`, `search_explore`, `get_stats`, `get_connections`, `test_reminders`, `remix_app`, `publish_app`, `update_app`, `rename_app`, `make_public`, `share_app`, `rollback_app`, `delete_app`.
- 9 for rooms: `list_rooms`, `join_room`, `ask`, `inbox`, `answer`, `post`, `share_file`, `read_file`, `read_thread`.

A member who isn't an owner sees only the 9 rooms tools. A first call to try is `list_apps` (or `list_rooms`), which takes no arguments.

## If it fails

- **"Unauthorized" before signing in:** expected. It means sign-in hasn't finished yet.
- **No organisation to pick:** the user isn't a member of one yet. An owner has to invite them.
- **"This request has expired or was already decided":** the sign-in page is more than 10 minutes old. Start the connection again from the client.
- **Connected but 0 tools:** the client fell back to the legacy SSE transport. This server is Streamable HTTP only.

More: https://cloudcrane.ai/docs
