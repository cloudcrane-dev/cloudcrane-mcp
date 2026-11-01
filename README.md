# CloudCrane MCP

[![CloudCrane MCP connector – tool definition quality and endpoint health on Glama](https://glama.ai/mcp/connectors/ai.cloudcrane/workspace/badges/score.svg)](https://glama.ai/mcp/connectors/ai.cloudcrane/workspace)

Ask your assistant for an app, and it lands on your phone's home screen.

Your assistant writes a small app (a habit tracker, a spending log, a workout
timer) and publishes it with [CloudCrane](https://cloudcrane.ai). Open the link
on your phone, add it to your home screen: its own icon, full screen, offline.

- **Your data stays on your phone.** An app reaches only the sites it lists, and you see them first.
- **Every app has a blueprint.** What it does, keeps and promises, in plain words.
- **Checked, and with testing on, proven.** No password or card fields, no undeclared camera or location; tests run in a real browser before a version is kept.
- **Every version is kept.** Change it by asking; go back to any version.
- **Build your own from anyone's.** Public apps are on [Explore](https://cloudcrane.ai/#explore).

## Connect

One remote MCP server, **Streamable HTTP** (stateless, no SSE stream), with
OAuth sign-in and dynamic client registration. Nothing to install.

```
https://cloudcrane.ai/api/apps/mcp
```

(`https://cloudcrane.ai/api/build/mcp`, the older address, is the same server.)

Add the URL with no key. The client opens a CloudCrane page where you sign in
(Google or an emailed code) and pick your workspace. Once you connect, the
assistant can see, make, change, roll back and delete your apps. You can sign an
assistant out at any time in the dashboard.

Sign-in has been tested with **Claude**, **ChatGPT**, **Cursor** and **Cline**. Exact settings for each are in [`llms-install.md`](llms-install.md), which an
agent can follow to set it up.

**Claude:** Settings → Connectors → Add custom connector with the URL above.

**ChatGPT:** add a custom MCP server with the URL above and choose *OAuth*.

**Cursor** (`~/.cursor/mcp.json`):

```json
{ "mcpServers": { "cloudcrane": { "url": "https://cloudcrane.ai/api/apps/mcp" } } }
```

**Cline** (`cline_mcp_settings.json`):

```json
{ "mcpServers": { "cloudcrane": { "transport": { "type": "streamableHttp", "url": "https://cloudcrane.ai/api/apps/mcp" } } } }
```

**Claude plugin:** this repo is also a plugin. It adds nothing but the connection to the server above: no hooks, scripts or local code. What you ask for, and the apps your assistant writes, go to CloudCrane so it can check and host them; what you save in an app stays on your phone.

**Gemini CLI** (an extension, from this repo):

```
gemini extensions install https://github.com/cloudcrane-dev/cloudcrane-mcp
```

Then run `/mcp auth cloudcrane` in Gemini CLI to sign in.

## Tools

| Tool | What it does |
|---|---|
| `list_apps` | Your apps, with their install links |
| `get_app` | An app's code |
| `find_in_app` | The lines of an app's code that match, to change part of a big app |
| `get_spec` | An app's blueprint and tests |
| `list_versions` | Every version, and which one is current |
| `list_remixes`, `get_remix` | Apps you chose to build your own of on Explore |
| `get_spec_guide` | How to write an app spec: the format, the catalog, worked examples |
| `check_publish` | A dry run of a publish: every problem, nothing stored |
| `publish_app` | A new app, with its blueprint; returns its install link |
| `update_app` | A new version: the whole file, or just the edits |
| `rename_app` | A new name; the address stays |
| `rollback_app` | Serves an earlier version again |
| `delete_app` | Deletes an app and every version |
| `publish_spec`, `update_spec` | An app from a [Blackbox](https://github.com/cloudcrane-dev/blackbox) spec: no code, built the same every time |

When it connects, the server tells the assistant how to write an app that
works here. Tools that change apps are marked so, and clients like Claude ask
before each call.

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
