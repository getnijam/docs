---
title: GitLab
description: Post a pass/fail commit status and a results note straight onto the merge request that triggered the run.
sidebar:
  order: 3
---

Connect GitLab to bring Nijam's results back to where the work happens - the merge request. Once
GitLab is connected, every run that comes from an MR pipeline gets a **commit status** (green when it
passes, red when it fails) and a **sticky note** summarizing the run, each linking back to the full
results and traces on Nijam.

Like Slack and GitHub, the GitLab integration is **outbound only**: Nijam posts statuses and notes. It
never runs or re-runs your pipelines, and it never reads your source code.

:::note
Connecting GitLab and changing its settings is **admin-only**. Members can view the configuration but
can't edit it. Org-level changes are applied when you click **Save changes**.
:::

## Connect GitLab

Connecting GitLab is an **organization-level, admin-only** action.

1. Open **Integrations** in the org sidebar and choose **GitLab → Connect GitLab**.
2. You'll be sent to gitlab.com to authorize Nijam.
3. You'll land back on the GitLab settings page in Nijam, connected.

Nijam is a GitLab **OAuth application** on gitlab.com. Self-managed instances aren't supported yet.

### What the connection grants

This is the one place GitLab differs meaningfully from GitHub, and it's worth reading before you
click connect.

GitLab has **no scope for writing commit statuses and notes alone**. Its only write-capable API scope
is `api`, which covers everything your GitLab account can reach. Nijam asks for that scope because
there is no narrower one, and only ever uses it to write commit statuses and merge-request notes.

Two consequences worth planning around:

- **Connect as a service account** if you'd rather not hand a broad grant tied to a person. A GitLab
  user with access to just the projects Nijam should post on keeps the grant proportional.
- **The connection belongs to whoever authorized it.** If that user loses access to a project, or
  their account is removed, posting stops and Nijam shows a **Reconnect** prompt. The settings page
  always names the connected user so it's never a mystery.

Unlike the GitHub App (which stores no secret at all), Nijam **does** store your GitLab tokens. They
are encrypted at rest with AES-256-GCM, never returned by the API, and never logged. The access token
is refreshed automatically when it expires.

## What gets posted

When a run that belongs to a merge request finishes, Nijam posts two things (each can be toggled
independently under the GitLab settings):

- **Commit status** - a `running` status appears while the tests run, then resolves to **success**
  (passed, or passed with flakes) or **failed**. The description carries the pass / fail / flaky
  counts and it links to the run on Nijam.
- **Merge request note** - a single results note is posted on the MR and **updated in place** on each
  new run, so the MR shows the latest result without a pile of stale notes. It includes the status,
  counts, duration, and a link to the full results and traces.

The status is named `Nijam · <project>` and posts against the pipeline's commit, so it appears
alongside your other pipeline statuses. The note is **sticky per merge request** - the same note is
edited each run rather than a new one added.

## Requirements

GitLab posting only kicks in when a run carries **merge-request context** - the MR IID and the
project path. The [`@nijam/pw-reporter`](/reporter/playwright/installation/) reads both automatically
from GitLab CI (`CI_MERGE_REQUEST_IID` and `CI_PROJECT_PATH`), so no extra configuration is needed
beyond connecting.

One thing to know: `CI_MERGE_REQUEST_IID` is set on **merge request pipelines**, not on plain branch
pipelines. A branch pipeline still gets a commit status if statuses are enabled, but no note, because
there is no merge request to attach one to.

## Per-project settings

By default every project posts to GitLab once the org is connected. In a project's
**Settings → GitLab**, you can:

- **Mute** the project entirely - no statuses or notes for its runs.
- Restrict posting to specific **branches** (see below).

Projects with no override inherit the org default (post on every branch).

## Branch filtering

To cut the noise, open a project's **Settings → GitLab → Branches** and list the branches you care
about (e.g. `main`, `release`). Once the list is non-empty, only runs on those branches post -
everything else stays quiet.

- Matching is **exact** against the run's detected branch. A run with no branch is skipped whenever a
  filter is set.
- Leave the list empty to post on every branch.

Branch filtering is per-project and **admin-only**.

## Projects

The GitLab settings page lists the projects the connected user is a member of. To change what Nijam
can post on, change that user's project membership in GitLab - Nijam reflects whatever access they
have.

## Using GitHub and GitLab together

An org can connect both. Nijam routes each run to the provider its CI came from, so a GitHub Actions
run posts to GitHub and a GitLab CI run posts to GitLab.

If a run comes from neither (a local run, or a CI provider Nijam doesn't recognize) and **both**
providers are connected, Nijam posts to neither, because the repository slug alone can't tell
`group/project` from `owner/repo`. With only one connected, that one posts as usual.

## Disconnecting

**Disconnect** (admin-only) deletes Nijam's copy of your tokens and stops all posting. The
authorization itself stays listed under **Preferences → Applications** in GitLab until you revoke it
there. Your per-project GitLab settings are kept, so reconnecting later restores them.
