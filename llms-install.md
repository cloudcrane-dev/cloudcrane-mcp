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
2. Picks the workspace. They must be an owner of it.
3. Clicks **Connect**. The agent can then see, make, change, roll back and delete their apps.

## Checking it worked

The server should show as connected with **26 tools**: `list_apps`, `get_app`, `find_in_app`, `look_at_app`, `try_app`, `diff_versions`, `get_problems`, `get_spec`, `list_versions`, `list_remixes`, `get_remix`, `search_explore`, `get_stats`, `get_connections`, `get_spec_guide`, `get_design_guide`, `get_port_guide`, `check_publish`, `publish_app`, `update_app`, `publish_spec`, `update_spec`, `rename_app`, `rollback_app`, `delete_app`, `remix_app`, and 5 prompts. A first call to try is `list_apps`, which takes no arguments.

## If it fails

- **"Unauthorized" before signing in:** expected. It means sign-in hasn't finished yet.
- **No workspace to pick:** the user doesn't own one. A workspace owner has to connect it.
- **"This request has expired or was already decided":** the sign-in page is more than 10 minutes old. Start the connection again from the client.
- **Connected but 0 tools:** the client fell back to the legacy SSE transport. This server is Streamable HTTP only.

More: https://cloudcrane.ai/docs
