# Installing the CloudCrane MCP server (for AI agents)

This is a **remote** MCP server. There is nothing to download, build or run:
add one entry to the client's MCP settings and sign in.

- **URL:** `https://cloudcrane.ai/api/build/mcp`
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
        "url": "https://cloudcrane.ai/api/build/mcp"
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
    "cloudcrane": { "url": "https://cloudcrane.ai/api/build/mcp" }
  }
}
```

## What the user does in the browser

1. Signs in to CloudCrane (Google or an emailed code), if not already signed in. Signing up is free at https://cloudcrane.ai.
2. Picks the workspace. They must be an owner of it.
3. Chooses what the agent may do: **Read only** (the default) or **Read and build**, which also makes and changes their apps (offered only if the client asked for it).
4. Clicks **Connect**.

## Checking it worked

The server should show as connected with **7 tools** (read only: `list_apps`, `get_app`, `find_in_app`, `get_spec`, `list_versions`, `list_remixes`, `get_remix`) or **14 tools** (read and build: also `publish_app`, `update_app`, `rename_app`, `rollback_app`, `delete_app`, `publish_spec`, `update_spec`). A first call to try is `list_apps`, which takes no arguments.

## If it fails

- **"Unauthorized" before signing in:** expected. It means sign-in hasn't finished yet.
- **No workspace to pick:** the user doesn't own one. A workspace owner has to connect it.
- **"This request has expired or was already decided":** the sign-in page is more than 10 minutes old. Start the connection again from the client.
- **Connected but 0 tools:** the client fell back to the legacy SSE transport. This server is Streamable HTTP only.

More: https://cloudcrane.ai/docs
