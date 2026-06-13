---
title: GitHub
description: Post a pass/fail status check and a results comment straight onto the pull request that triggered the run.
sidebar:
  order: 2
---

Connect GitHub to bring Nijam's results back to where the work happens — the pull request. Once the
Nijam GitHub App is installed, every run that comes from a PR gets a **status check** (green when it
passes, red when it fails) and a **sticky comment** summarizing the run, each linking back to the full
results and traces on Nijam.

Like Slack, the GitHub integration is **outbound only**: Nijam posts checks and comments. It never
runs or re-runs your tests, and it never reads your source code.

:::note
Connecting GitHub and changing its settings is **admin-only**. Members can view the configuration but
can't edit it. Org-level changes are applied when you click **Save changes**.
:::

## Connect GitHub

Connecting GitHub is an **organization-level, admin-only** action.

1. Open **Integrations** in the org sidebar and choose **GitHub → Connect**.
2. You'll be sent to GitHub to **install the Nijam GitHub App** on the organization or account that
   owns your repositories. Grant it access to **all repositories** or just the ones you want Nijam to
   post on.
3. You'll land back on the GitHub settings page in Nijam, connected.

Nijam stores **no GitHub secret**. Instead of a long-lived token, the GitHub App mints short-lived,
installation-scoped access tokens on demand whenever it needs to post — so there's nothing sensitive
to leak. The App only needs permission to write checks and pull-request comments.

## What gets posted

When a run that belongs to a pull request finishes, Nijam posts two things (each can be toggled
independently under the GitHub settings):

- **PR status check** — an in-progress check appears while the tests run, then resolves to **green**
  (passed, or passed with flakes) or **red** (failed). The check title carries the pass / fail / flaky
  counts and links to the run on Nijam.
- **PR comment** — a single results comment is posted on the PR and **updated in place** on each new
  run, so the PR shows the latest result without a pile of stale comments. It includes the status,
  counts, duration, and a link to the full results and traces.

The status check posts on the **head commit of the PR**, so it shows up next to your other required
checks. The comment is **sticky per pull request** — the same comment is edited each run rather than a
new one added.

## Requirements

GitHub posting only kicks in when a run carries **pull-request context** — the PR number and the
`owner/repo`. The [`@nijam/pw-reporter`](/reporter/playwright/installation/) detects both automatically from your
CI environment (e.g. GitHub Actions `pull_request` / `pull_request_target` events), so no extra
configuration is needed beyond installing the App. Runs without PR context (a plain push to `main`,
say) still get a status check on their commit if checks are enabled, but no comment.

## Per-project settings

By default every project posts to GitHub once the org is connected. In a project's
**Settings → GitHub**, you can:

- **Mute** the project entirely — no checks or comments for its runs.
- Restrict posting to specific **branches** (see below).

Projects with no override inherit the org default (post on every branch).

## Branch filtering

To cut the noise, open a project's **Settings → GitHub → Branches** and list the branches you care
about (e.g. `main`, `release`). Once the list is non-empty, only runs on those branches post —
everything else stays quiet.

- Matching is **exact** against the run's detected branch. A run with no branch is skipped whenever a
  filter is set.
- Leave the list empty to post on every branch.

Branch filtering is per-project and **admin-only**.

## Repositories

The GitHub settings page lists the repositories the Nijam App can currently access. To add or remove
repositories, **manage the installation on GitHub** (the App's settings) — Nijam reflects whatever
access you've granted.

## Disconnecting

**Disconnect** (admin-only) removes the connection from Nijam and stops all posting. To fully revoke
access, also **uninstall the Nijam App** from your GitHub organization's settings. Your per-project
GitHub settings are kept, so reconnecting later restores them.
