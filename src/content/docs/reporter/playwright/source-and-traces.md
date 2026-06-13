---
title: Source & traces
description: What the Playwright reporter uploads — test source, error logs, and failure traces — and how to control it.
sidebar:
  order: 5
---

Beyond results, the Playwright reporter uploads the things that make the dashboard genuinely useful: your
**test source**, the **error log and failing line** of each failure, and — unique to Playwright — **failure
traces** (with screenshots and video). Everything here is fail-soft and non-blocking.

## Test source

So the **test detail** page can render each test inline with its run history (instead of only linking out
to your repo), the reporter uploads the source of each spec file the run executed. This is **on by
default**.

- Only the spec files the run actually executed are uploaded — **never your application code**.
- Paths are relative to the project root (Playwright's `rootDir`).
- Files over **256 KB are skipped**.
- Uploads are capped at **4 concurrent**, and any read/upload error is logged as a `[nijam]` warning and
  ignored.

### Opting out

If your test files are sensitive, opt out with `uploadSource: false`:

```ts title="playwright.config.ts"
reporter: [
  ['@nijam/pw-reporter', {
    apiKey: process.env.NIJAM_API_KEY,
    projectId: '<your-project-uuid>',
    uploadSource: false, // ← don't send spec source to Nijam
  }],
],
```

With source off, the dashboard still links to the test at the run's commit via your provider
(GitHub/GitLab/Bitbucket) — you just don't get the inline render.

## Failure detail

Each failure carries its **error log/stack** and the **failing line**, so the test detail page tells you
what broke without leaving Nijam.

## Traces

Traces are uploaded **only for tests that fail or time out** — matching Playwright's default
`on-first-retry` trace mode. They:

- stream straight to storage and **never block your tests**,
- are capped at **4 concurrent** uploads to spare CI bandwidth.

Make sure tracing is enabled in your Playwright config so there's a trace to upload:

```ts title="playwright.config.ts"
export default defineConfig({
  use: {
    trace: 'on-first-retry', // Playwright's default — failures get a trace
  },
});
```

Screenshots and video captured by Playwright are uploaded alongside the trace.

## Bandwidth and privacy summary

| Uploaded                  | When                          | Limits                                  |
| ------------------------- | ----------------------------- | --------------------------------------- |
| Test results + metadata   | every run                     | —                                       |
| Error log + failing line  | every failure                 | —                                       |
| Test source               | every run (opt-out)           | skip > 256 KB; 4 concurrent; tests only |
| Traces                    | failed/timed-out tests only   | 4 concurrent; streamed                  |

Everything here is **fail-soft**: if any upload fails, the reporter warns and moves on. Your CI run's
pass/fail result is never affected.
