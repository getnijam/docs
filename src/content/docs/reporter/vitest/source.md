---
title: Source
description: What the Vitest reporter uploads — test source and error logs — and how to control it.
sidebar:
  order: 5
---

Beyond results, the Vitest reporter uploads the things that make the dashboard genuinely useful: your
**test source** and the **error log / stack and failing line** of each failure. Everything here is
fail-soft and non-blocking.

## Test source

So the **test detail** page can render each test inline with its run history (instead of only linking out
to your repo), the reporter uploads the source of each test file the run executed. This is **on by
default**.

- Only the test files the run actually executed are uploaded — **never your application code**.
- Paths are relative to the Vitest root.
- Files over **256 KB are skipped**.
- Uploads are capped at **4 concurrent**, and any read/upload error is logged as a `[nijam]` warning and
  ignored.

### Opting out

If your test files are sensitive, opt out with `uploadSource: false`:

```ts title="vitest.config.ts"
new NijamReporter({
  apiKey: process.env.NIJAM_API_KEY,
  projectId: '<your-project-uuid>',
  uploadSource: false, // ← don't send test source to Nijam
});
```

With source off, the dashboard still links to the test at the run's commit via your provider
(GitHub/GitLab/Bitbucket) — you just don't get the inline render.

## Failure detail

Each failure carries its **error log / stack** and the **failing line**, so the test detail page tells you
what broke without leaving Nijam. Capturing the failing line requires `includeTaskLocation: true` in your
Vitest config (see [Configuration](/reporter/vitest/configuration/)).

## Flakiness

Vitest's built-in `retry` reruns are reported to Nijam, which is how a test gets scored as flaky. See
[Flakiness](/concepts/flakiness/).

## Bandwidth and privacy summary

| Uploaded                  | When                | Limits                                  |
| ------------------------- | ------------------- | --------------------------------------- |
| Test results + metadata   | every run           | —                                       |
| Error log + failing line  | every failure       | —                                       |
| Test source               | every run (opt-out) | skip > 256 KB; 4 concurrent; tests only |

Everything here is **fail-soft**: if any upload fails, the reporter warns and moves on. Your CI run's
pass/fail result is never affected.
