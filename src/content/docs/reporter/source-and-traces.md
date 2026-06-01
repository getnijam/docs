---
title: Source & traces
description: What the reporter uploads — spec source and failure traces — and how to control it.
sidebar:
  order: 5
---

Beyond results, the reporter uploads two things that make the dashboard genuinely useful: your **spec
source** and your **failure traces**. Both are fail-soft and non-blocking.

## Spec source

So the **test detail** page can render each test inline with its run history (instead of only linking out
to your repo), the reporter uploads the source of each spec file Playwright ran. This is **on by default**.

- Only the `*.spec` / `*.test` files Playwright actually ran are uploaded — **never your application
  code**.
- Paths are relative to Playwright's `rootDir`.
- Files over **256 KB are skipped**.
- Uploads are capped at **4 concurrent**, and any read/upload error is logged as a `[nijam]` warning and
  ignored.

### Opting out

If your spec files are sensitive, opt out:

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

## Bandwidth and privacy summary

| Uploaded                | When                  | Limits                                  |
| ----------------------- | --------------------- | --------------------------------------- |
| Test results + metadata | every run             | —                                       |
| Spec source             | every run (opt-out)   | skip > 256 KB; 4 concurrent; specs only |
| Traces                  | failed/timed-out only | 4 concurrent; streamed                  |

Everything here is **fail-soft**: if any upload fails, the reporter warns and moves on. Your CI run's
pass/fail result is never affected.
