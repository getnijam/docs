---
title: Environments
description: Tag each run with its deploy target and filter the dashboard by it.
sidebar:
  order: 4
---

The `environment` option (pytest: `nijam_environment`) tags each run with the target it ran against — any
string you like: `"staging"`, `"production"`, `"pr-preview"`, and so on. The dashboard then offers an
**environment filter** so you can scope runs (and flakiness) to one target.

## Setting it

Most teams wire it to an env var so each pipeline reports its own target. The Playwright example below maps
1:1 to Vitest's `environment` constructor option and pytest's `nijam_environment` ini option:

```ts title="playwright.config.ts"
reporter: [
  ['@nijam/pw-reporter', {
    apiKey: process.env.NIJAM_API_KEY,
    projectId: '<your-project-uuid>',
    environment: process.env.DEPLOY_ENV, // or "staging", process.env.NODE_ENV, etc.
  }],
],
```

It's free-form — Nijam doesn't validate it against a fixed set. Pick a convention and stick to it so the
filter stays tidy.

## Unset runs

Runs reported **without** an `environment` are grouped under **Unset** in the dashboard. That's a feature,
not an error — you can always tell which runs carried no environment info, and filter them out.

## Why it matters for flakiness

A test that's "flaky everywhere" and one that's "flaky only on `staging`" are different problems. Tagging
environments lets Nijam — and you — separate the two when reading a test's history. See
[Flakiness](/concepts/flakiness/).
