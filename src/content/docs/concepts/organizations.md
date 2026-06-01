---
title: Organizations
description: How organizations, members, seats, and invitations work in Nijam.
sidebar:
  order: 1
---

An **organization** is the top-level container in Nijam. It holds your [projects](/concepts/projects/),
your runs, your team, and your billing. When you sign in, you land on a picker of the organizations you
belong to.

## Members and permissions

Organizations have a **flat permission model**: there are no roles. Every member of an organization has
every permission — they can create and delete projects, invite and remove members, and manage billing.

:::note
This is intentional for how teams use Nijam today. If you need stricter separation, create separate
organizations.
:::

## Seats

Each member counts as one **seat**. Your plan sets the seat limit:

- **Free** — up to **2 seats**.
- **Pro** — **unlimited seats**.

See [Plans](/billing/plans/) for the full comparison.

## Invitations

Invite teammates by **email** from the organization's members screen. They receive an invite link; when
they accept (signing in or creating an account with the invited email) they join the org. An invitation
is tied to the email it was sent to — accepting it from a different account shows a "wrong account"
prompt so you can switch.

## Creating organizations

Every user can create up to **2 organizations**. This keeps automated/abusive sign-ups in check; it isn't
a limit on how many orgs you can *belong* to (you can be a member of any number via invitations).

## Billing scope

Billing is **per organization** — each org has its own plan, usage cycle, and invoice. Upgrading one org
to Pro doesn't change another. Manage it from the org's [Billing](/billing/plans/) screen.
