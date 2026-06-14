---
title: Plans
description: Free and Pro — what's included, what they cost, and how to upgrade.
sidebar:
  order: 1
---

Nijam has two plans, billed **per organization**. The free plan is a real, usable tier; Pro lifts the caps
and meters usage beyond a generous included allowance.

Usage is measured in **credits**, which are framework-weighted because pytest/Vitest suites produce far
more (and cheaper) tests than Playwright E2E:

- 1 credit = 1 Playwright test report
- 1 credit = 100 pytest test reports
- 1 credit = 100 Vitest test reports

## At a glance

|                        | Free                          | Pro — **$20/mo**                                        |
| ---------------------- | ----------------------------- | ------------------------------------------------------- |
| Credits / cycle        | **1,000** (hard cap, blocks)  | **10,000 included**, then **$0.001 / credit**, no block |
| Overage                | —                             | Metered, billed in arrears on your next invoice         |
| Seats (members)        | **3**                         | **Unlimited**                                           |
| Trace & run retention  | **7 days**                    | **90 days**                                             |
| Billing cycle          | Org anniversary               | Your subscription's billing period                      |

A credit is the billable unit — a Playwright test costs 1, a pytest/Vitest test costs 0.01. See
[Runs & tests](/concepts/runs-and-tests/#what-counts-toward-usage).

## Free

Everything you need to evaluate Nijam on a real suite:

- Up to **1,000 credits per cycle** (e.g. 1,000 Playwright tests, or up to 100,000 pytest/Vitest tests).
  When you hit the cap, further uploads are **blocked** until the cycle resets — your tests still run and
  pass/fail normally; they just aren't recorded until reset.
- **3 seats** and **7-day** retention.

## Pro — $20/mo

For teams running Nijam in CI day to day:

- **10,000 credits included** each cycle.
- Beyond that, **$0.001 per credit** (that's **$10 per additional 10,000**, charged proportionally — 5,000
  over = $5, 25,000 over = $25).
- **Pro never blocks.** Usage past the included amount is metered and billed in arrears on your next
  invoice, alongside the $20 base.
- **Unlimited seats** and **90-day** retention.

:::tip[Early-bird offer]
The **$0.001 / credit** metered rate is an early-bird price — **half** the standard **$0.002**. Register
now as a Pro org and that half-price usage rate is locked in for **at least 2 years**. The $20 base is
unchanged.
:::

See [Usage & billing cycles](/billing/usage/) for exactly how the meter and cycle work.

## Who can manage billing

Billing actions and amounts are **admin-only** (see [Roles and
permissions](/concepts/organizations/#roles-and-permissions)). Admins upgrade, manage the subscription,
and see charges; members can open the Billing screen to view **usage** (credits and seats) but not amounts.
When metered overage is in effect, members see a "contact an admin" note instead of a figure.

## Upgrading

An **admin** opens the organization's **Billing** screen and chooses **Upgrade**. Checkout, invoices, and
payment are handled by [Polar](https://polar.sh) (our merchant of record) — you'll get a hosted checkout
and a customer portal to manage the subscription and download invoices.

## Downgrading / canceling

Cancel from the same Billing screen (via the Polar customer portal). When the subscription ends, the org
returns to **Free** — the 1,000/cycle cap and 2-seat / 7-day limits apply again.
