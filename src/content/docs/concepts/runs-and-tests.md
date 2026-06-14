---
title: Runs & tests
description: How a test run maps to Nijam, and what counts toward your usage.
sidebar:
  order: 3
---

## Runs

A **run** is one test-run invocation (e.g. `playwright test`, `pytest`, or `vitest run`). When the suite
finishes, the reporter ships the run to Nijam with everything needed to place it in history:

- **Results** — every test that executed, with its status (passed, failed, timed out, skipped) and
  duration.
- **Git & CI metadata** — commit, branch, PR number, CI run id, CI run URL, and the git author
  (email + name). On supported CI providers these are [detected automatically](/reporter/playwright/ci-integration/).
- **Environment** — the optional [`environment`](/reporter/playwright/environments/) tag, or **Unset** if you didn't
  pass one.

Runs are listed newest-first in the dashboard, and you can filter by branch, environment, and status.

## Tests

Within a run, each test is a **test result**. Across runs, results for the same test accumulate into that
test's **history** — the timeline that tells you whether a failure is new, recurring, or
[flaky](/concepts/flakiness/). The test detail page shows that history alongside the test's source and its
latest failure detail (and trace, on Playwright).

## Retries

Retries are reported as part of the run. A test that fails then passes on retry is recorded as such — which
is exactly the signal Nijam uses to flag flakiness. Each framework has its own retry mechanism: Playwright
`retries`, Vitest `retry`, and pytest via the [`pytest-rerunfailures`](https://pypi.org/project/pytest-rerunfailures/)
plugin.

## What counts toward usage

Your plan's allowance is measured in **credits**, summed across all runs in a billing cycle. Credits are
framework-weighted: **1 credit = 1 Playwright test = 100 pytest/Vitest tests**. A Playwright run with 200
tests contributes 200 credits; the same number of pytest/Vitest tests contributes 2. Retries that store
an additional result count too.

- **Free** caps at **1,000 credits per cycle** and blocks further uploads once reached.
- **Pro** includes **10,000 per cycle**, then bills overage per credit — it never blocks.

See [Usage & billing cycles](/billing/usage/) for the details.

## Deleting a run

**Admins** can delete an individual run from its **⋮ menu** in the runs list (you confirm by typing
`delete`). This permanently removes the run, its test results, and its stored artifacts (traces,
screenshots, videos) — it can't be undone. Members can't delete runs (see [Roles and
permissions](/concepts/organizations/#roles-and-permissions)). To remove an entire project at once,
see [Deleting a project](/concepts/projects/#deleting-a-project).
