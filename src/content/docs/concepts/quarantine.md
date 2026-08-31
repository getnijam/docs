---
title: Quarantine
description: Park a known-bad test so its failures stop marking runs as failed, without hiding it or losing its history.
sidebar:
  order: 5
---

Detection on its own is just a report. Nijam tells you which tests are [flaky](/concepts/flakiness/),
but until you act on that, the suite is still red and your team is still ignoring it.

The usual fix is `test.skip()` in the repo. That needs a pull request, it stops the test running, and
nobody ever revisits it. Eighteen months later there is a pile of skipped tests and no one remembers
which ones still matter.

**Quarantine** moves that decision into Nijam, where it stays visible and reversible.

## What it does

A quarantined test **still runs** and **still records results**. What changes is only how its failures
count:

- The run is **not marked as failed** when its only failures are quarantined.
- The run shows as a **pass** across your run history and pass-rate stats.
- The check Nijam posts to GitHub or GitLab **stays green**. If your team has that check set as
  required, that may unblock the pull request. Whether it does is your branch protection setting, not
  something Nijam decides.
- Slack notifications on the `fail` and `regression` triggers **do not fire** for a quarantined-only
  failure, because the build did not break.

A run with one real failure and one quarantined failure is still a **failure**. Quarantine subtracts
from the blocking count, it does not silence the run.

## It is never silent

Every surface that goes green because of a quarantine says so and names the tests:

- The pull request check and comment carry a **Quarantined, not counted as a failure** block.
- The GitLab commit status reads `Passed, 2 quarantined tests failed`.
- The run in Nijam renders as green-but-not-clean, never as a plain pass.
- Any member of the organization can see the full quarantine list, even though only admins can change
  it.

That is deliberate. A green check that quietly hides a broken test is worse than a red one.

## Why it does not skip the test

Quarantine is a verdict, not a `skip`. Nijam keeps running the test on purpose, for two reasons.

A test that stops running can never prove it is healthy again. Because quarantined tests keep
reporting, Nijam can tell you *"this has passed 14 runs in a row, take it out of quarantine"*. That
prompt is the whole difference between a ledger and a graveyard, and a `test.skip()` in your repo can
never give it to you.

It also means quarantine needs **no reporter change** and works identically for Playwright, pytest,
and Vitest.

## Using it

Quarantine a test from anywhere it appears: the run's file view, the test explorer, the flaky list, the
failing list, or the test detail page. **Organization admins only**, since it changes whether future
runs are recorded as failures.

The full list lives under **Test explorer, Quarantined**, showing each test, who quarantined it and
when, an optional reason, and how it has behaved on recent runs. Removing a test is a single click; its
failures count normally again from the next run.

## What it does not do

- It is **not retroactive**. Quarantining changes how *subsequent* runs are judged. Checks and comments
  already posted are never rewritten.
- It does not change the recorded result of the test itself. The test failed, the run detail says so,
  and the failure count still includes it.
