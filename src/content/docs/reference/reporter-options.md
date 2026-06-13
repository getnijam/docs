---
title: Reporter options
description: The complete reporter options reference for Playwright, pytest, and Vitest.
sidebar:
  order: 1
---

The full option set for `@nijam/pw-reporter`, passed as the second element of the reporter tuple in
`playwright.config.ts`. For guided setup, see [Configuration](/reporter/playwright/configuration/). The pytest and
Vitest reporters expose the [same options](#pytest-and-vitest-equivalents) under their own naming.

## Options (Playwright)

| Option         | Type      | Required | Default                 | Description                                                                                  |
| -------------- | --------- | -------- | ----------------------- | -------------------------------------------------------------------------------------------- |
| `apiKey`       | `string`  | yes      | —                       | API key from the Nijam dashboard. Store as a CI secret.                                       |
| `projectId`    | `string`  | yes      | —                       | Project ID (UUID) from the dashboard.                                                         |
| `apiUrl`       | `string`  | no       | `https://api.nijam.dev` | API base URL. Defaults to the hosted Nijam API — you don't normally need to set this.         |
| `environment`  | `string`  | no       | —                       | Free-form deploy tag (e.g. `"staging"`). Adds a dashboard filter; omitted runs show **Unset**. |
| `uploadSource` | `boolean` | no       | `true`                  | Upload spec source for inline rendering. `false` to opt out.                                  |
| `silent`       | `boolean` | no       | `false`                 | Suppress all `[nijam]` log lines.                                                            |

## Type

```ts
interface NijamReporterOptions {
  apiKey: string;
  projectId: string;
  apiUrl?: string;       // default: https://api.nijam.dev
  environment?: string;
  uploadSource?: boolean; // default: true
  silent?: boolean;       // default: false
}
```

## pytest and Vitest equivalents

The pytest and Vitest reporters take the **same options** as Playwright — only the naming and where you set
them differ.

| Playwright option | pytest ini option     | Vitest constructor option |
| ----------------- | --------------------- | ------------------------- |
| `apiKey`          | `nijam_api_key`       | `apiKey`                  |
| `projectId`       | `nijam_project_id`    | `projectId`               |
| `apiUrl`          | `nijam_api_url`       | `apiUrl`                  |
| `environment`     | `nijam_environment`   | `environment`             |
| `uploadSource`    | `nijam_upload_source` | `uploadSource`            |
| `silent`          | `nijam_silent`        | `silent`                  |
| —                 | `nijam_auto_complete` | `autoComplete`            |

- **pytest** — set ini options in `pytest.ini` / `[tool.pytest.ini_options]`; `NIJAM_API_KEY` can also come
  from the environment. The plugin auto-loads, so there's no code to write.
- **Vitest** — pass options to `new NijamReporter({ … })` in `vitest.config.ts`. Vitest also needs
  `test.includeTaskLocation: true` so the reporter can capture the failing line.

## Behavior reference

| Behavior            | Value                                                                |
| ------------------- | -------------------------------------------------------------------- |
| Failure mode        | Fail-soft — logs a `[nijam]` warning, never throws, never blocks CI. |
| Peer dependency     | `@playwright/test` >= 1.40.0 (Playwright-specific)                   |
| Node engine         | >= 18 (Playwright/Vitest reporters)                                  |
| Test source max size | 256 KB (larger files skipped)                                       |
| Upload concurrency  | 4 (source and traces each)                                           |
| Traces uploaded for | failed / timed-out tests only                                       |
| License             | MIT                                                                  |

## Related

- [Installation](/reporter/playwright/installation/)
- [Configuration](/reporter/playwright/configuration/)
- [CI variables](/reference/ci-variables/)
