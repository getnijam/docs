---
title: Environments
description: Tag each pytest run with its deploy target and filter the dashboard by it.
sidebar:
  order: 4
---

The `nijam_environment` ini option tags each run with the target it ran against — any string you like:
`staging`, `production`, `pr-preview`, and so on. The dashboard then offers an **environment filter** so
you can scope runs (and flakiness) to one target.

## Setting it

Set it in `pytest.ini`, or wire it to the `NIJAM_ENVIRONMENT` env var so each pipeline reports its own
target (the env var wins when both are set):

```ini title="pytest.ini"
[pytest]
nijam_project_id = <your-project-uuid>
nijam_environment = staging
```

```sh
# or override per pipeline
export NIJAM_ENVIRONMENT=production
```

It's free-form — Nijam doesn't validate it against a fixed set. Pick a convention and stick to it so the
filter stays tidy.

## Unset runs

Runs reported **without** an environment are grouped under **Unset** in the dashboard. That's a feature,
not an error — you can always tell which runs carried no environment info, and filter them out.

## Why it matters for flakiness

A test that's "flaky everywhere" and one that's "flaky only on `staging`" are different problems. Tagging
environments lets Nijam — and you — separate the two when reading a test's history. See
[Flakiness](/concepts/flakiness/).
