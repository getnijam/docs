---
title: Your own database
description: Connect your own Postgres so runs, projects, and test results are stored in your database.
sidebar:
  order: 2
---

With bring-your-own-database, your projects, runs, test results, and integration settings are stored in
**your** Postgres. Nijam keeps only accounts, organizations, billing, and API keys.

## Requirements

- A **Postgres** database (any host: RDS, Cloud SQL, Neon, Supabase, self-managed, etc.).
- The database is reachable at a **public address** (Nijam's servers connect to it). Private, loopback,
  and link-local hosts are rejected.
- A role whose connection string has privileges to **create tables** (Nijam runs its schema migration
  on connect) and to read and write those tables thereafter.
- The organization has **no projects yet** (see the [overview](/byoc/#requirements)).

## Prepare a database

Create a dedicated, empty database (recommended) and a role for Nijam:

```sql
CREATE DATABASE nijam;
CREATE ROLE nijam_app WITH LOGIN PASSWORD 'a-strong-password';
GRANT ALL PRIVILEGES ON DATABASE nijam TO nijam_app;
-- Nijam owns the tables it creates in this database, so it needs schema (CREATE) rights:
\c nijam
GRANT ALL ON SCHEMA public TO nijam_app;
```

Use a database dedicated to Nijam. Nijam creates and manages its own tables there; it never touches
tables it did not create.

## Connect

1. Go to **Settings, Bring your own cloud, Database**.
2. Paste the **connection string**, for example:

   ```
   postgresql://nijam_app:a-strong-password@db.example.com:5432/nijam?sslmode=require
   ```

   Use `sslmode=require` (or stronger) so the connection is encrypted in transit.
3. Click **Test** to verify Nijam can connect, then **Connect**.

On connect, Nijam runs its schema migration in your database and begins storing all new runs and
projects there.

## What Nijam stores

Nijam creates a small set of tables (`projects`, `runs`, `test_executions`, `test_attachments`,
`spec_sources`, and the per-project integration settings) and migrates them forward automatically as
Nijam evolves. Object storage keys for artifacts are recorded here; the artifact bytes live in your
[storage](/byoc/storage/) (or Nijam's managed storage if you have not enabled BYO-storage).

## Rotating and disabling

- **Rotate**: re-enter a new connection string and click **Connect**.
- **Disable**: click **Disable database**. New data goes to Nijam's managed database; the data already
  in your Postgres stays there and stops appearing in Nijam until you reconnect.
