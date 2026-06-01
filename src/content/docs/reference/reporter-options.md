---
title: Reporter options
description: The complete @nijam/pw-reporter options reference.
sidebar:
  order: 1
---

The full option set for `@nijam/pw-reporter`, passed as the second element of the reporter tuple in
`playwright.config.ts`. For guided setup, see [Configuration](/reporter/configuration/).

## Options

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

## Behavior reference

| Behavior            | Value                                                                |
| ------------------- | -------------------------------------------------------------------- |
| Failure mode        | Fail-soft — logs a `[nijam]` warning, never throws, never blocks CI. |
| Peer dependency     | `@playwright/test` >= 1.40.0                                         |
| Node engine         | >= 18                                                                |
| Spec source max size | 256 KB (larger files skipped)                                       |
| Upload concurrency  | 4 (source and traces each)                                           |
| Traces uploaded for | failed / timed-out tests only                                       |
| License             | MIT                                                                  |

## Related

- [Installation](/reporter/installation/)
- [Configuration](/reporter/configuration/)
- [CI variables](/reference/ci-variables/)
