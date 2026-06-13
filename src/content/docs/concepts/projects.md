---
title: Projects
description: What a project is, where to find its ID and API key, and how runs roll up into it.
sidebar:
  order: 2
---

A **project** is a single test suite tracked by Nijam — Playwright, pytest, or Vitest — usually one per
repository. It owns the runs, tests, artifacts, and history for that suite. An
[organization](/concepts/organizations/) can hold as many projects as you need.

## Project ID

Every project has a stable **Project ID** (a UUID) shown in its settings. The
[reporter](/reporter/playwright/configuration/) uses it to route uploads to the right project:

```ts
['@nijam/pw-reporter', {
  apiKey: process.env.NIJAM_API_KEY,
  projectId: 'b4fdfc06-76a2-4721-89eb-9d070add8a5a', // ← your project's UUID
}]
```

The Project ID isn't a secret — it identifies *where* runs go. The **API key** is what authorizes the
upload, so that's the one you keep in CI secrets.

## What a project tracks

- **Runs** — one per test-run invocation (e.g. `playwright test`, `pytest`, `vitest run`). See
  [Runs & tests](/concepts/runs-and-tests/).
- **Tests** — each test accumulates a history across runs, which powers
  [flakiness scoring](/concepts/flakiness/).
- **Failure detail** — the error log and failing line for each failure, plus Playwright traces for
  failed/timed-out tests.
- **Test source** — the test files the run executed, so the test detail view renders inline (opt-out with
  [`uploadSource: false`](/reporter/playwright/source-and-traces/)).
- **Environments** — an optional [tag](/reporter/playwright/environments/) per run (`staging`, `production`, …)
  you can filter by.

## One project or many?

A good default is **one project per repository**. If a single repo runs several independent suites you
want to track separately, give each its own project (and its own `projectId`).

## Deleting a project

An **admin** can delete a project from its **Settings → Danger zone** (you confirm by typing the project's
name). This permanently removes the project and **everything in it** — every run, test result, and stored
artifact (traces, screenshots, videos) — and can't be undone. Deleting individual **runs** is also
admin-only; see [Runs & tests](/concepts/runs-and-tests/#deleting-a-run).
