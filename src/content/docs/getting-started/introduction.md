---
title: Introduction
description: What Nijam is, the problem it solves, and how the pieces fit together.
sidebar:
  order: 1
---

**Nijam is test analytics for Playwright, pytest, and Vitest.** It takes the runs your CI already produces
and turns them into history you can reason about — which tests are flaky, which ones regressed, and the
failure detail behind every red build. Think Sentry, but for your tests.

## The problem

A single test report tells you about **one run**. It can't tell you that a test has failed 3 of the last 20
times, that it only breaks on one browser or environment, or that it started flaking after a specific
commit. That context lives across runs — and it's exactly what you need to decide whether a red build is a
real regression or just noise.

## How it works

Nijam has three pieces:

1. **The reporter** — one per framework: [`@nijam/pw-reporter`](/reporter/playwright/installation/) for Playwright
   (`playwright.config.ts`), [`pytest-nijam`](/reporter/playwright/installation/) for pytest, and
   [`@nijam/vitest-reporter`](/reporter/playwright/installation/) for Vitest. After each run it ships the results
   (plus the failing line, error log, and test source — and traces, on Playwright) to the Nijam API. It's
   **fail-soft**: if Nijam is unreachable, it logs a `[nijam]` warning and gets out of the way — it will
   never break your CI.
2. **The API** — receives runs, stores history, scores flakiness, and keeps your artifacts. The ingestion
   endpoints are identical across frameworks. Hosted at `https://api.nijam.dev`.
3. **The dashboard** — [nijam.dev](https://www.nijam.dev), where you browse runs, drill into a test's
   history, read failures (and watch Playwright traces), and manage your team and billing.

```
Playwright / pytest / Vitest CI  ──(reporter)──▶  Nijam API  ──▶  Dashboard
   test run            results + failing line          history,        run lists,
                       + error log + source            flakiness        test detail,
                       (+ traces on Playwright)        scoring          failures
```

## What you get

- **Run history** — every run stored with its commit, branch, PR, CI link, and git author.
- **Flakiness scoring** — tests that pass and fail on the same code are detected and ranked.
- **Failure detail** — the error log and the failing line for every framework; plus Playwright traces for
  failed/timed-out tests, streamed straight to storage.
- **Inline source** — the test that produced a result, rendered alongside its run history.
- **Environments** — tag each run (`staging`, `production`, …) and filter the dashboard by target.

## Next step

Head to the [Quickstart](/getting-started/quickstart/) to go from sign-up to your first run.
