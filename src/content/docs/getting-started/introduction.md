---
title: Introduction
description: What Nijam is, the problem it solves, and how the pieces fit together.
sidebar:
  order: 1
---

**Nijam is test analytics for Playwright.** It takes the runs your CI already produces and turns them into
history you can reason about — which tests are flaky, which ones regressed, and the traces behind every
failure. Think Sentry, but for your Playwright suite.

## The problem

A Playwright report tells you about **one run**. It can't tell you that a test has failed 3 of the last 20
times, that it only breaks on `webkit`, or that it started flaking after a specific commit. That context
lives across runs — and it's exactly what you need to decide whether a red build is a real regression or
just noise.

## How it works

Nijam has three pieces:

1. **The reporter** — [`@nijam/pw-reporter`](/reporter/installation/), a Playwright reporter you add to
   `playwright.config.ts`. After each run it ships the results (plus traces and spec source) to the Nijam
   API. It's **fail-soft**: if Nijam is unreachable, it logs a `[nijam]` warning and gets out of the way —
   it will never break your CI.
2. **The API** — receives runs, stores history, scores flakiness, and keeps your traces. Hosted at
   `https://api.nijam.dev`.
3. **The dashboard** — [nijam.dev](https://www.nijam.dev), where you browse runs, drill into a test's
   history, watch traces, and manage your team and billing.

```
Playwright CI  ──(@nijam/pw-reporter)──▶  Nijam API  ──▶  Dashboard
   test run            results +              history,        run lists,
                       traces + source        flakiness        test detail,
                                              scoring          traces
```

## What you get

- **Run history** — every run stored with its commit, branch, PR, CI link, and git author.
- **Flakiness scoring** — tests that pass and fail on the same code are detected and ranked.
- **Failure traces** — Playwright traces for failed/timed-out tests, streamed straight to storage.
- **Inline source** — the spec that produced a test, rendered alongside its run history.
- **Environments** — tag each run (`staging`, `production`, …) and filter the dashboard by target.

## Next step

Head to the [Quickstart](/getting-started/quickstart/) to go from sign-up to your first run.
