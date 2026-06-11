---
title: MCP server
description: Let AI agents query your Playwright runs, failures, and flakiness through the Model Context Protocol.
sidebar:
  order: 3
---

The Nijam **MCP server** lets any [Model Context Protocol](https://modelcontextprotocol.io)–capable
agent — Claude Code, Cursor, Codex, Claude Desktop, and others — answer questions about your test
runs straight from your real history:

> *"Why is the checkout suite red?"* · *"Is `cart updates quantity` flaky?"* · *"When did this test
> start failing?"*

The agent calls the server, the server reads your data from the Nijam API, and the agent reasons over
the result. It is **read-only**: it can pull runs, failures, and flakiness, but can never upload,
change settings, or touch anything else.

:::note
The MCP server runs **locally**, as a subprocess your MCP client launches over stdio. Your API key
lives only on your machine — it is never sent to the model or the model provider. Only the test data
the agent asks for is returned to it as context.
:::

## Prerequisites

You need a Nijam **read key** (`nij_rk_…`). Read keys are a distinct, read-only credential — separate
from the `nij_sk_…` [ingestion keys](/reporter/configuration/) your CI uses, so a leaked CI key can
never expose data and a read key can never pollute it.

1. Open **Secret keys** in your organization settings.
2. In the **Read keys (MCP)** section, choose **Create key**.
3. Copy the `nij_rk_…` token — it's shown once.

Read keys are always **org-wide**, so the agent can resolve any project in the organization by name
or slug on its own.

## Set up your client

Pick your agent below. In every case, replace `nij_rk_...` with the read key you created.

### Claude Code

```sh
claude mcp add nijam -e NIJAM_API_KEY=nij_rk_... -- npx -y @nijam/mcp-server
```

### Cursor

Add to `~/.cursor/mcp.json` (or `.cursor/mcp.json` in a project):

```json
{
  "mcpServers": {
    "nijam": {
      "command": "npx",
      "args": ["-y", "@nijam/mcp-server"],
      "env": { "NIJAM_API_KEY": "nij_rk_..." }
    }
  }
}
```

### Codex

Add to `~/.codex/config.toml`:

```toml
[mcp_servers.nijam]
command = "npx"
args = ["-y", "@nijam/mcp-server"]
env = { NIJAM_API_KEY = "nij_rk_..." }
```

### Claude Desktop

Add to `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "nijam": {
      "command": "npx",
      "args": ["-y", "@nijam/mcp-server"],
      "env": { "NIJAM_API_KEY": "nij_rk_..." }
    }
  }
}
```

`npx -y @nijam/mcp-server` fetches and runs the server on demand — there's nothing to install or keep
updated. Point it at a self-hosted API with the optional `NIJAM_API_URL` env var (defaults to
`https://api.nijam.dev`).

## What the agent can do

The server exposes nine read tools. `get_projects` is the entry point — it lists the projects the key
can see; every other tool accepts a project **id, slug, or name**, so you never paste a UUID.

| Tool | Answers |
| --- | --- |
| `get_projects` | What projects can I see? (resolves "my project" to an id/slug) |
| `get_latest_run` | What's the state of the latest run, optionally on a branch? |
| `list_runs` | Run history, filterable by status and branch |
| `get_run` | One run's stats and per-spec-file breakdown |
| `list_failing_tests` | Which tests failed in a run, and with what error |
| `get_failure` | Full detail of one failure: every attempt, error, and artifact links |
| `is_test_flaky` | A verdict, with evidence, on whether a test is flaky |
| `get_test_history` | One test across the last 30 runs — spot when it broke |
| `list_flaky_tests` | The flakiest tests in a project, ranked |

Trace artifacts come back as short-lived signed links the agent can open, minted on demand.

## Security

- **Read-only.** Read keys cannot write — the upload routes reject them — so the MCP server can never
  create, change, or delete anything.
- **Scoped.** A key sees only its organization's projects, and anything outside its scope returns a
  plain 404.
- **Local.** The server is a local subprocess; the key never leaves your machine, and it is never
  exposed to the model.
- **Revocable.** Revoke a read key anytime from **Secret keys** — agents using it lose access
  immediately.
