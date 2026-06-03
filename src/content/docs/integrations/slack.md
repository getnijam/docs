---
title: Slack
description: Auto-post passing, flaky, and failing run results to your team's Slack channels.
sidebar:
  order: 1
---

Connect Slack to push run results into the channels your team already watches. Every message links
back to the run on Nijam — no copy-pasting CI URLs, no refreshing the dashboard to find out a run
broke.

Slack notifications are **outbound only**: Nijam posts summaries and links. It never runs or re-runs
your tests.

:::note
Changing any Slack setting is **admin-only**. Members can view the configuration and the live preview
but can't edit it. Edits are applied when you click **Save changes** — nothing saves per-click.
:::

## Connect a workspace

Connecting Slack is an **organization-level, admin-only** action.

1. Open **Integrations** in the org sidebar and choose **Add to Slack**.
2. Authorize the Nijam app in your Slack workspace.
3. You'll land back on the Slack settings page — pick a **default channel** to start posting.

The connection stores a Slack bot token, encrypted at rest. Nijam only ever posts messages and reads
your channel list; it can't read your conversations.

## When Nijam posts

Choose one **trigger** for the whole organization under **When to post**:

| Trigger | Posts when… |
| --- | --- |
| **Every run** | every run finishes |
| **Only failures** | a run has one or more failed tests |
| **Failures or flaky** | a run has failed **or** flaky tests (the default) |
| **Regressions only** | the first failure after a passing run on the same branch |

Notifications fire when a run is **finalized** — once, after the last shard reports.

## Channel routing

- **Default channel** — where every project's runs post.
- **Per-project override** — in a project's **Settings → Slack notifications**, point that project at a
  different channel, or **mute** it entirely. Projects with no override inherit the org default.

## Styling

Under **Styling**:

- **Layout** — **Block Kit** (Slack's modern layout) or **Classic** (adds a colored status bar).
- **Detail level** — **Compact** shows status and pass / fail / flaky counts; **Full** also lists each
  failed and flaky test with its file, title, and error.

A **live preview** beside the settings shows exactly what each message will look like for passing,
flaky, and failing runs.

## Test the connection

Use **Send test message** on the Slack settings page to post a sample message to your default channel
and confirm everything is wired up.

## Disconnecting

**Disconnect** (admin-only) revokes the Slack token and stops all posting. Your channel and trigger
settings are kept, so reconnecting later restores them.
