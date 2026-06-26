---
title: Configuration
description: Every Nijam Vitest reporter option, with defaults and examples.
sidebar:
  order: 2
---

The Nijam Vitest reporter is configured by passing options to the `NijamReporter` constructor in
`vitest.config.ts`. Only `apiKey` and `projectId` are required; everything else has a sensible default.
Remember to set `includeTaskLocation: true` on `test` so Nijam can capture the failing line.

## Minimal

```ts title="vitest.config.ts"
import { defineConfig } from 'vitest/config';
import NijamReporter from '@nijam/vitest-reporter';

export default defineConfig({
  test: {
    includeTaskLocation: true,
    reporters: [
      'default',
      new NijamReporter({
        apiKey: process.env.NIJAM_API_KEY,
        projectId: 'b4fdfc06-76a2-4721-89eb-9d070add8a5a',
      }),
    ],
  },
});
```

## Full

```ts title="vitest.config.ts"
new NijamReporter({
  apiKey: process.env.NIJAM_API_KEY,
  projectId: 'b4fdfc06-76a2-4721-89eb-9d070add8a5a',
  apiUrl: process.env.NIJAM_API_URL,   // default: https://api.nijam.dev
  environment: process.env.DEPLOY_ENV, // free-form tag, e.g. "staging"
  uploadSource: true,                  // upload test source for inline rendering
  autoComplete: true,                  // finalize the run when the suite ends
  silent: false,                       // suppress [nijam] log lines
});
```

## Options

All options are passed to the `NijamReporter` constructor.

| Option         | Required | Default                 | Description                                                                                         |
| -------------- | -------- | ----------------------- | --------------------------------------------------------------------------------------------------- |
| `apiKey`       | yes      | -                       | API key from the Nijam dashboard. Store it as a CI secret.                                           |
| `projectId`    | yes      | -                       | The project's ID (UUID) from the dashboard.                                                          |
| `apiUrl`       | no       | `https://api.nijam.dev` | API base URL. Also settable via `NIJAM_API_URL`. You don't normally need to set this.                |
| `environment`  | no       | -                       | Free-form deploy tag (e.g. `"staging"`). Adds a run filter. Runs without it show as **Unset**.       |
| `uploadSource` | no       | `true`                  | Upload test source so the dashboard renders tests inline. Set `false` to opt out.                   |
| `autoComplete` | no       | `true`                  | Finalize the run when the suite ends. Set `false` (or `NIJAM_AUTO_COMPLETE=false`) for fan-out CI.   |
| `silent`       | no       | `false`                 | Suppress all `[nijam]` log lines.                                                                    |

For `environment`, see [Environments](/reporter/vitest/environments/). For `uploadSource`, see
[Source](/reporter/vitest/source/). For `autoComplete`, see
[CI integration](/reporter/vitest/ci-integration/).

## Where do I get these?

- **`projectId`** - open your project in the [dashboard](https://www.nijam.dev) and copy its Project ID.
- **`apiKey`** - generate one in the project's settings and set it as `NIJAM_API_KEY` in your CI secrets.

## Fail-soft behavior

The reporter is built to **never break your CI**. If the API is unreachable, the key is wrong, or anything
else goes sideways, it logs a `[nijam]` warning and lets the run finish exactly as it would without Nijam.
Set `silent: true` to suppress even those warnings.

## CI metadata

You don't configure commit/branch/PR/author - the reporter [auto-detects](/reporter/vitest/ci-integration/)
them from your CI provider's environment variables, falling back to `git`.
