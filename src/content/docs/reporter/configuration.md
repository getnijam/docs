---
title: Configuration
description: Every Nijam reporter option, with defaults and examples.
sidebar:
  order: 2
---

The examples here use the Playwright reporter, configured inline in `playwright.config.ts`. Only `apiKey`
and `projectId` are required; everything else has a sensible default. The pytest reporter (`pytest.ini`
ini options) and Vitest reporter (`NijamReporter` constructor) accept the **same options** under their own
naming — see [Installation](/reporter/installation/) and the
[reporter options reference](/reference/reporter-options/#pytest-and-vitest-equivalents).

## Minimal

```ts title="playwright.config.ts"
reporter: [
  ['@nijam/pw-reporter', {
    apiKey: process.env.NIJAM_API_KEY,
    projectId: 'b4fdfc06-76a2-4721-89eb-9d070add8a5a',
  }],
],
```

## Full

```ts title="playwright.config.ts"
reporter: [
  ['@nijam/pw-reporter', {
    apiKey: process.env.NIJAM_API_KEY,
    projectId: 'b4fdfc06-76a2-4721-89eb-9d070add8a5a',
    apiUrl: process.env.NIJAM_API_URL,   // default: https://api.nijam.dev
    environment: process.env.DEPLOY_ENV, // free-form tag, e.g. "staging"
    uploadSource: true,                  // upload spec source for inline rendering
    silent: false,                       // suppress [nijam] log lines
  }],
],
```

## Options

| Option         | Required | Default                 | Description                                                                                         |
| -------------- | -------- | ----------------------- | --------------------------------------------------------------------------------------------------- |
| `apiKey`       | yes      | —                       | API key from the Nijam dashboard. Store it as a CI secret.                                           |
| `projectId`    | yes      | —                       | The project's ID (UUID) from the dashboard.                                                          |
| `apiUrl`       | no       | `https://api.nijam.dev` | API base URL. Defaults to the hosted Nijam API — you don't normally need to set this.                |
| `environment`  | no       | —                       | Free-form deploy tag (e.g. `"staging"`). Adds a run filter. Runs without it show as **Unset**.       |
| `uploadSource` | no       | `true`                  | Upload spec source so the dashboard renders tests inline. Set `false` to opt out.                   |
| `silent`       | no       | `false`                 | Suppress all `[nijam]` log lines.                                                                    |

For `environment`, see [Environments](/reporter/environments/). For `uploadSource` and trace behavior, see
[Source & traces](/reporter/source-and-traces/).

## Where do I get these?

- **`projectId`** — open your project in the [dashboard](https://www.nijam.dev) and copy its Project ID.
- **`apiKey`** — generate one in the project's settings and set it as `NIJAM_API_KEY` in your CI secrets.

## Fail-soft behavior

The reporter is built to **never break your CI**. If the API is unreachable, the key is wrong, or anything
else goes sideways, it logs a `[nijam]` warning and lets the run finish exactly as it would without Nijam.
Set `silent: true` to suppress even those warnings.

## CI metadata

You don't configure commit/branch/PR/author — the reporter [auto-detects](/reporter/ci-integration/) them
from your CI provider's environment variables, falling back to `git`.
