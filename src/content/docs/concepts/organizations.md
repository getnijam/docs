---
title: Organizations
description: How organizations, roles and permissions, members, seats, and invitations work in Nijam.
sidebar:
  order: 1
---

An **organization** is the top-level container in Nijam. It holds your [projects](/concepts/projects/),
your runs, your team, and your billing. When you sign in, you land on a picker of the organizations you
belong to.

## Roles and permissions

Every member of an organization has one of two roles:

- **Admin** — full control. Admins manage people (invite, remove, change roles), manage billing (upgrade,
  see charges, manage the subscription), edit organization settings, delete runs, and delete the
  organization.
- **Member** — full access to the day-to-day work. Members view everyone and everything, and create and
  edit projects, runs, and secret keys. They **can't** manage people, billing, or organization settings.

The person who **creates** an organization becomes its first admin. Admins promote or demote members from
the **Users** screen (pick a role from the dropdown next to each member).

### Who can do what

| Action                                   | Admin | Member |
| ---------------------------------------- | :---: | :----: |
| View members, projects, runs, and usage  |   ✓   |   ✓    |
| Create & edit projects                   |   ✓   |   ✓    |
| Create & revoke secret keys              |   ✓   |   ✓    |
| Delete a project                         |   ✓   |   —    |
| Delete a run                             |   ✓   |   —    |
| Invite / remove members, change roles    |   ✓   |   —    |
| Manage billing & see charges             |   ✓   |   —    |
| Edit organization settings (name, logo)  |   ✓   |   —    |
| Delete the organization                  |   ✓   |   —    |

Members still open the **Users** and **Billing** screens — read-only. On Billing they see usage (tests and
seats) but never amounts; when metered overage is in effect they're shown a "contact an admin" note
instead of a figure.

:::note
An organization must always have at least one admin — you can't remove or demote the last one.
:::

## Seats

Each member counts as one **seat**. Your plan sets the seat limit:

- **Free** — up to **2 seats**.
- **Pro** — **unlimited seats**.

See [Plans](/billing/plans/) for the full comparison.

## Invitations

**Admins** invite teammates by **email** from the organization's **Users** screen, choosing whether the
invitee joins as a **member** (the default) or an **admin**. They receive an invite link; when they accept
(signing in or creating an account with the invited email) they join the org with that role. An invitation
is tied to the email it was sent to — accepting it from a different account shows a "wrong account"
prompt so you can switch.

## Creating organizations

Every user can create up to **2 organizations**. This keeps automated/abusive sign-ups in check; it isn't
a limit on how many orgs you can *belong* to (you can be a member of any number via invitations).

## Deleting an organization

An **admin** can delete an organization from **Settings → Danger zone** (you confirm by typing the
organization's name). Deletion permanently removes the org and everything attached to it — members,
pending invitations, and the logo.

You must **delete all of its projects first** — an organization with projects can't be deleted. This is a
guard against wiping a lot of run history behind a single click.

## Billing scope

Billing is **per organization** — each org has its own plan, usage cycle, and invoice. Upgrading one org
to Pro doesn't change another. Only **admins** can manage it, from the org's [Billing](/billing/plans/)
screen.
