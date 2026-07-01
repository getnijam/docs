---
title: Configuration
description: Every Nijam pytest reporter option, with defaults and examples.
sidebar:
  order: 2
---

The Nijam pytest plugin is configured with ini options under `[pytest]` in `pytest.ini` (or
`[tool.pytest.ini_options]` in `pyproject.toml`). Every ini key is also overridable by an environment
variable, and **the environment variable wins** when both are set - handy for keeping secrets out of the
repo and varying per-pipeline values in CI. Only the API key and `nijam_project_id` are required;
everything else has a sensible default. There are **no test-code changes**.

## Minimal

```ini title="pytest.ini"
[pytest]
nijam_project_id = b4fdfc06-76a2-4721-89eb-9d070add8a5a
```

```sh
export NIJAM_API_KEY=...
```

## Full

```ini title="pytest.ini"
[pytest]
nijam_project_id = b4fdfc06-76a2-4721-89eb-9d070add8a5a
nijam_environment = staging   ; free-form deploy tag
nijam_upload_source = true    ; upload test source for inline rendering
nijam_auto_complete = true    ; finalize the run when the suite ends
nijam_silent = false          ; suppress [nijam] log lines
; nijam_api_key / nijam_api_url are normally set via env vars instead
```

## Options

Each ini key has a matching environment variable; the env var takes precedence.

| ini key               | Env var               | Required | Default                 | Description                                                                                |
| --------------------- | --------------------- | -------- | ----------------------- | ------------------------------------------------------------------------------------------ |
| `nijam_api_key`       | `NIJAM_API_KEY`       | yes      | -                       | API key from the Nijam dashboard. Set it as a CI secret via the env var.                    |
| `nijam_project_id`    | `NIJAM_PROJECT_ID`    | yes      | -                       | The project's ID (UUID) from the dashboard.                                                 |
| `nijam_api_url`       | `NIJAM_API_URL`       | no       | `https://api.nijam.dev` | API base URL. You don't normally need to set this.                                          |
| `nijam_environment`   | `NIJAM_ENVIRONMENT`   | no       | -                       | Free-form deploy tag (e.g. `staging`). Adds a run filter. Runs without it show as **Unset**.|
| `nijam_upload_source` | -                     | no       | `true`                  | Upload test source so the dashboard renders tests inline. Set `false` to opt out.          |
| `nijam_auto_complete` | `NIJAM_AUTO_COMPLETE` | no       | `true`                  | Finalize the run when the suite ends. Set `false` for fan-out CI.                           |
| `nijam_silent`        | -                     | no       | `false`                 | Suppress all `[nijam]` log lines.                                                           |

For `nijam_environment`, see [Environments](/reporter/pytest/environments/). For `nijam_upload_source`,
see [Source](/reporter/pytest/source/). For `nijam_auto_complete`, see
[CI integration](/reporter/pytest/ci-integration/).

## Where do I get these?

- **`nijam_project_id`** - open your project in the [dashboard](https://www.nijam.dev) and copy its
  Project ID.
- **API key** - generate one in the project's settings and set it as `NIJAM_API_KEY` in your CI secrets.

## Fail-soft behavior

The plugin is built to **never break your CI**. If the API is unreachable, the key is wrong, or anything
else goes sideways, it logs a `[nijam]` warning and lets the run finish exactly as it would without Nijam.
Set `nijam_silent = true` to suppress even those warnings.

## CI metadata

You don't configure commit/branch/PR/author - the plugin [auto-detects](/reporter/pytest/ci-integration/)
them from your CI provider's environment variables, falling back to `git`.

## Re-run only failed tests

`pytest-nijam` also installs a `nijam-pytest` CLI. `nijam-pytest fetch-failed` asks Nijam which tests
failed in the previous run (by nodeid) so a retry runs only those, and clubs the attempts into one run on
the dashboard:

```bash
nijam-pytest fetch-failed --output failed.txt --export-env "$GITHUB_ENV"
pytest $(cat failed.txt)
```

It reuses your `NIJAM_API_KEY` and `NIJAM_PROJECT_ID`. See
[Re-run only failed tests](/guides/rerun-failed-tests/) for the full per-provider CI recipe.
