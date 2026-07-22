---
title: Bring your own cloud
description: Store your runs, test results, and artifacts in your own Postgres and object storage, and skip metered usage.
sidebar:
  order: 1
---

Bring your own cloud (BYOC) keeps your test data in **your** infrastructure. Instead of storing runs
and artifacts on Nijam's managed database and object storage, you point Nijam at your own Postgres and
your own S3, Google Cloud Storage, or Azure bucket. Nijam becomes the application layer; the data lives
with you.

BYOC is a **Pro capability**, not a separate plan. When you enable it, your **metered usage is
waived**: you pay the flat Pro base with no per-test metering, because your test data lives in your
own cloud and the storage cost is yours.

## What lives where

| Stays with Nijam (control) | Moves to your cloud (with BYOC) |
| --- | --- |
| Accounts, users, sessions | Projects |
| Organizations and membership | Runs and test results |
| Billing | Traces, screenshots, videos |
| API keys (ingest / read) | Slack / GitHub integration settings |

Nijam never moves your identity or billing data. Everything about your **testing operations** can move
to your database and storage.

You can enable the two independently: bring your own **database**, your own **storage**, or both. Each
falls back to Nijam's managed default until you turn it on.

## Requirements

- Your organization is on **Pro** (BYOC is a Pro feature; enabling it waives your metered usage).
- You are an **organization admin**.
- The organization has **no projects yet**. BYOC is set up at onboarding so no existing data is left
  behind on the managed cloud. If you already have data and want to migrate, [contact us](mailto:support@nijam.dev).
- Your database and storage are reachable at a **public address**. Private, loopback, and link-local
  hosts are rejected (Nijam's servers must be able to connect).

## Enable it

1. Go to **Settings, Bring your own cloud**.
2. Configure the **[Database](/byoc/database/)** and/or **[Storage](/byoc/storage/)** sections.
3. Use **Test** to verify the connection, then **Connect**.

On connect, Nijam tests the connection, creates its schema in your database (for BYO-database), and
routes all new runs and artifacts to your cloud.

## Turning it off

Disabling a section (or the whole feature) reverts new data to Nijam's managed cloud. **Your existing
data stays in your own cloud, untouched.** It simply stops appearing in Nijam until you reconnect.

## Security

Connection strings and cloud credentials are **encrypted at rest** and are never shown again in the
dashboard or returned by the API. Grant Nijam the **least privilege** it needs (see the per-provider
guides). You can rotate credentials at any time by re-entering them.
