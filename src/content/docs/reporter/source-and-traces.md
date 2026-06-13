---
title: Source & traces
description: What the reporter uploads — test source, error logs, and (for Playwright) failure traces — and how to control it.
sidebar:
  order: 5
---

Beyond results, the reporter uploads the things that make the dashboard genuinely useful: your **test
source**, the **error log and failing line** of each failure, and — **for Playwright only** — **failure
traces** (with screenshots and video). Everything here is fail-soft and non-blocking.

:::note
**Traces, screenshots, and video are Playwright-only.** pytest and Vitest have no trace concept — they
report the error log and the failing line for each failure, with no traces. Test-source upload and error
logs apply to all three frameworks.
:::

## Test source

So the **test detail** page can render each test inline with its run history (instead of only linking out
to your repo), the reporter uploads the source of each test file the run executed. This is **on by
default** for all three frameworks.

- Only the test files the run actually executed are uploaded — **never your application code**.
- Paths are relative to the project root (Playwright's `rootDir`, the pytest rootdir, or the Vitest root).
- Files over **256 KB are skipped**.
- Uploads are capped at **4 concurrent**, and any read/upload error is logged as a `[nijam]` warning and
  ignored.

### Opting out

If your test files are sensitive, opt out — `uploadSource: false` (Playwright/Vitest) or
`nijam_upload_source = false` (pytest):

```ts title="playwright.config.ts"
reporter: [
  ['@nijam/pw-reporter', {
    apiKey: process.env.NIJAM_API_KEY,
    projectId: '<your-project-uuid>',
    uploadSource: false, // ← don't send test source to Nijam
  }],
],
```

With source off, the dashboard still links to the test at the run's commit via your provider
(GitHub/GitLab/Bitbucket) — you just don't get the inline render.

## Failure detail

For **every framework**, each failure carries its **error log/stack** and the **failing line**, so the
test detail page tells you what broke without leaving Nijam.

## Traces (Playwright only)

On Playwright, traces are uploaded **only for tests that fail or time out** — matching Playwright's default
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

pytest and Vitest produce no traces; their failures are captured via the error log and failing line above.

## Bandwidth and privacy summary

| Uploaded                  | When                                   | Limits                                  |
| ------------------------- | -------------------------------------- | --------------------------------------- |
| Test results + metadata   | every run                              | —                                       |
| Error log + failing line  | every failure (all frameworks)         | —                                       |
| Test source               | every run (opt-out)                    | skip > 256 KB; 4 concurrent; tests only |
| Traces                    | failed/timed-out only (Playwright only)| 4 concurrent; streamed                  |

Everything here is **fail-soft**: if any upload fails, the reporter warns and moves on. Your CI run's
pass/fail result is never affected.
