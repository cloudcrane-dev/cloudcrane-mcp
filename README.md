# CloudCrane MCP

[![smithery badge](https://smithery.ai/badge/cloudcrane/workspace)](https://smithery.ai/servers/cloudcrane/workspace)
[![CloudCrane MCP connector – tool definition quality and endpoint health on Glama](https://glama.ai/mcp/connectors/ai.cloudcrane/workspace/badges/score.svg)](https://glama.ai/mcp/connectors/ai.cloudcrane/workspace)

Ask your assistant for an app, and it lands on your phone's home screen.

Your assistant writes a small app for you (a habit tracker, a spending log, a
packing checklist, a workout timer) and publishes it with
[CloudCrane](https://cloudcrane.ai). You get a link. Open it on your phone, add
it to your home screen, and it's an app of its own: its own icon, full screen,
working offline.

- **Your data stays on your phone.** What you save in an app is kept on the
  phone, never on our servers.
- **Change it by asking.** "Add a reading habit", "make the buttons bigger":
  your assistant updates the app, and the new version arrives the next time
  you open it, with everything you saved still there.
- **Every version is kept.** Go back to any earlier version, or delete an app
  for good.
- **Each app is locked down.** Every app runs at its own address, can't talk
  to other websites, and is checked before it's published: no password or card
  fields, no forms that send your entries anywhere.

## Connect

One remote MCP server, **Streamable HTTP** (stateless, no SSE stream), with
OAuth sign-in and dynamic client registration. Nothing to install.

```
https://cloudcrane.ai/api/build/mcp
```

(`https://cloudcrane.ai/api/apps/mcp` is the same server.)

Add the URL with no key. The client opens a CloudCrane page where you sign in
(Google or an emailed code), pick your workspace and choose what the assistant
may do: **Read only**, or **Read and build**, which also lets it make and change
your apps. You can sign an assistant out at any time from **My apps** in the
dashboard.

Sign-in has been tested with **Claude**, **ChatGPT**, **Cursor**, **Cline** and
**Smithery**. Exact settings for each are in [`llms-install.md`](llms-install.md), which an
agent can follow to set it up.

**Claude:** Settings → Connectors → Add custom connector with the URL above.

**ChatGPT:** add a custom MCP server with the URL above and choose *OAuth*.

**Cursor** (`~/.cursor/mcp.json`):

```json
{ "mcpServers": { "cloudcrane": { "url": "https://cloudcrane.ai/api/build/mcp" } } }
```

**Cline** (`cline_mcp_settings.json`):

```json
{ "mcpServers": { "cloudcrane": { "transport": { "type": "streamableHttp", "url": "https://cloudcrane.ai/api/build/mcp" } } } }
```

## Tools

| Tool | What it does | Needs |
|---|---|---|
| `list_apps` | Your apps, with their install links | Read |
| `get_app` | An app's code, to read or change it | Read |
| `list_versions` | Every version of an app, and which one is installed | Read |
| `publish_app` | Publishes a new app and returns its install link | Build |
| `update_app` | Publishes a new version of an app | Build |
| `rollback_app` | Serves an earlier version again | Build |
| `delete_app` | Deletes an app and every version of it | Build |

The server tells the assistant how to write an app that works here when it
connects: one self-contained HTML file, mobile first, data kept on the phone,
and saved data carried across every update. The tools that change apps are
marked as such, so clients like Claude ask you before each call.

## Try

1. "Make me a habit tracker for water, reading and the gym, and put it on my phone."
2. "Add a 'walk 20 minutes' habit to my Habits app."
3. "My packing list app: go back to the version from before the last change."

## Things that look like a broken server

- **406:** MCP requires `Accept: application/json, text/event-stream` on every POST, even though this server never sends a stream.
- **405 on GET:** the server is stateless and offers no SSE stream, so only POST is allowed. A client that silently falls back to SSE connects but lists no tools.
- **403:** a request from a browser (any request with an `Origin` header) is refused. Call it from a server or a desktop client.

## Links

- [CloudCrane](https://cloudcrane.ai)
- [Docs](https://cloudcrane.ai/docs)

This repository holds documentation and the registry entry (`server.json`), not
the server's source.
