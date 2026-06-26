---
title: Flakiness
description: How Nijam detects and ranks flaky tests across Playwright, pytest, and Vitest.
sidebar:
  order: 4
---

A **flaky** test is one that passes and fails **without the code changing** - the classic source of "just
re-run it" CI noise. Nijam's whole reason for collecting history is to surface these automatically.

## How Nijam spots it

Because every run is stored with its commit, Nijam can compare outcomes for the **same test on the same
code**. Two signals feed the score:

- **Within a run** - a test that fails and then passes on a **retry** is flaky by definition. (Retries are
  per-framework: Playwright `retries`, Vitest `retry`, pytest via the `pytest-rerunfailures` plugin -
  without retries there's no within-run flaky signal.)
- **Across runs** - a test whose result flips between pass and fail for the same (or unchanged) commit,
  over its recent history.

Tests are then **ranked** so the noisiest ones rise to the top - you fix what's costing you the most
re-runs first, instead of guessing.

## Flaky vs. regressed

Nijam distinguishes two failure shapes, because they need different responses:

- **Flaky** - inconsistent on unchanged code. Usually a timing/race/environment issue in the test.
- **Regressed** - consistently started failing at a specific commit. Usually a real bug.

The test detail page shows the history timeline so you can tell which one you're looking at.

## Getting better signal

Flakiness detection improves with history, so:

- **Run on CI, not just locally** - consistent environment and metadata make the comparison meaningful.
- **Keep retries on** - Playwright `retries`, Vitest `retry`, or pytest's `pytest-rerunfailures` give
  Nijam the within-run flip signal.
- **Tag environments** - an [`environment`](/reporter/playwright/environments/) tag lets you tell apart "flaky
  everywhere" from "flaky only on one browser/`staging`".
