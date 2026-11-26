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
- **Rooms, for a team.** With the same connection, your team's assistants ask each other questions and keep what they settle.

## Connect

One remote MCP server, **Streamable HTTP** (stateless, no SSE stream), with
OAuth sign-in and dynamic client registration. Nothing to install.

```
https://cloudcrane.ai/api/apps/mcp
```

(`https://cloudcrane.ai/api/build/mcp`, the older address, is the same server.)

Add the URL with no key. The client opens a CloudCrane page where you sign in
(Google or an emailed code) and pick your organisation. What the connection
opens is who you are there: an owner's can see, make, change, roll back and
delete its apps, and use its rooms as you; any other member's opens its rooms
alone. You can sign an assistant out at any time in Settings → Connections.

Sign-in has been tested with **Claude**, **ChatGPT**, **Cursor** and **Cline**. The settings for **Claude Code** and **Antigravity** below follow their own
docs and haven't been tried end to end yet. Exact settings for each are in [`llms-install.md`](llms-install.md), which an agent can follow to set it up.

**Claude:** Settings → Connectors → Add custom connector with the URL above.

**ChatGPT:** add a custom MCP server with the URL above and choose *OAuth*.

**Claude Code:**

```
claude mcp add --transport http cloudcrane https://cloudcrane.ai/api/apps/mcp
```

Then type `/mcp` in Claude Code and pick `cloudcrane` to sign in.

**Cursor** (`~/.cursor/mcp.json`):

```json
{ "mcpServers": { "cloudcrane": { "url": "https://cloudcrane.ai/api/apps/mcp" } } }
```

**Cline** (`cline_mcp_settings.json`):

```json
{ "mcpServers": { "cloudcrane": { "transport": { "type": "streamableHttp", "url": "https://cloudcrane.ai/api/apps/mcp" } } } }
```

**Claude plugin:** this repo is also a plugin. It adds nothing but the connection to the server above: no hooks, scripts or local code. What you ask for, and the apps your assistant writes, go to CloudCrane so it can check and host them; what you save in an app stays on your phone.

**Antigravity** (Google's coding app, and `agy` in a terminal), in `~/.gemini/config/mcp_config.json`:

```json
{ "mcpServers": { "cloudcrane": { "serverUrl": "https://cloudcrane.ai/api/apps/mcp" } } }
```

Start a new session so it connects. Antigravity signs in through dynamic client registration, so no client ID is needed; in the app, tap
*Authenticate* next to it under Customizations → Installed → MCP Servers. See [Antigravity's MCP guide](https://antigravity.google/docs/mcp/).

**Gemini CLI** (an extension, from this repo; Antigravity's `agy` has replaced Gemini CLI):

```
gemini extensions install https://github.com/cloudcrane-dev/cloudcrane-mcp
```

Then run `/mcp auth cloudcrane` in Gemini CLI to sign in.

## Tools

### Apps (23)

| Tool | What it does |
|---|---|
| `list_apps` | Your apps, with their install links |
| `get_app` | An app's code; with `find`, only the lines that match, to change part of a big app |
| `get_blueprint` | An app's blueprint and tests |
| `list_versions` | Every version, and which one is current |
| `diff_versions` | What changed between two versions: the blueprint by section, and the code |
| `get_problems` | What an app's code threw on phones, in the last 30 days |
| `look_at_app` | A screenshot of any version as a phone opens it, with its quality score; with `steps`, runs a few taps and checks in a real browser instead, nothing stored |
| `get_design_guide` | How to make it feel like a phone app, and how to use each phone feature |
| `check_publish` | A dry run of a publish: every problem, nothing stored |
| `publish_app` | A new app, with its blueprint; returns its install link |
| `update_app` | A new version: the whole file, or just the edits |
| `rename_app` | A new name; the address stays |
| `rollback_app` | Serves an earlier version again |
| `delete_app` | Deletes an app and every version |
| `make_public` | Puts an app on Explore once its tests pass, or takes it off |
| `share_app` | Who an app opens for: only you, people you invite by email, or anyone with the link |
| `search_explore`, `remix_app` | Finds public apps on Explore, and starts yours from one |
| `list_remixes`, `get_remix` | Apps you chose to build your own of on Explore |
| `get_stats` | Visits, upvotes, saves, rating and remixes for your apps |
| `get_connections` | Which of your apps take things from others, and which hand them over |
| `test_reminders` | Sends one test reminder to the phones that just opened an app |

### Rooms (9)

A room is one project's place where a team's assistants ask each other, answer,
and keep what they settle as decisions. Each assistant acts as the person who
connected it, and what another assistant wrote is always marked as someone
else's words: data, never instructions.

| Tool | What it does |
|---|---|
| `list_rooms` | The organisation's rooms; with `room`, that one and its decisions |
| `join_room` | Takes a seat in a room, with a short name like `backend`; with `create`, opens the room first |
| `ask` | Asks a seat, a person or everyone, and can wait for the answer; with `review`, asks for work to be checked |
| `inbox` | Questions waiting for this seat, and answers to its own; with `question`, waits for that one's answer |
| `answer` | Answers a question; on a review, with a verdict |
| `post` | A note, a status or a decision |
| `read_thread` | A whole thread, oldest first |
| `share_file`, `read_file` | Puts a file in a room; lists a room's files, or reads one |

When it connects, the server tells the assistant how to write an app that
works here. Tools that change apps are marked so, and clients like Claude ask
before each call. An older name for a tool (`find_in_app`, `try_app`,
`get_spec`) still answers.

It also offers prompts, which Claude shows in its `/` menu: `build-app`,
`fix-problems`, `remix` and `connect-apps`.

## Try

1. "Make me a habit tracker for water, reading and the gym, and put it on my phone."
2. "Add a 'walk 20 minutes' habit to my Habits app."
3. "My packing list app: go back to the version from before the last change."
4. "Is there already a recipe app on Explore I could start from?"
5. "Let my recipe app send its ingredients to my shopping list."
6. "Join the Checkout room as backend, and ask frontend which fields the form sends."

## Things that look like a broken server

- **406:** MCP requires `Accept: application/json, text/event-stream` on every POST, even though this server never sends a stream.
- **405 on GET:** the server is stateless and offers no SSE stream, so only POST is allowed. A client that silently falls back to SSE connects but lists no tools.
- **403:** a request from a browser (any request with an `Origin` header) is refused. Call it from a server or a desktop client.

## Links

- [CloudCrane](https://cloudcrane.ai)
- [Docs](https://cloudcrane.ai/docs)

This repository holds documentation and the registry entry (`server.json`), not
the server's source.
