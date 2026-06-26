---
title: Usage & billing cycles
description: How usage is counted, when cycles reset, and how Pro overage is metered and billed.
sidebar:
  order: 2
---

## What's measured

Usage is counted in **credits**, summed across every run in the current cycle. Credits are
framework-weighted: **1 credit = 1 Playwright test = 100 pytest/Vitest tests** (a Playwright test costs
1 credit; a pytest/Vitest test costs 0.01). A Playwright run with 200 tests adds 200 credits; the same
count of pytest tests adds 2. Retries that store another result count too. You can watch the current
total on your organization's **Billing** screen, which also shows the raw test count behind it.

:::note
Everyone in the org sees **usage** here. The **amounts** - the estimated charge, invoices, and billing
actions - are visible to **admins** only; members see a "contact an admin" note when metered overage is in
effect. See [Roles and permissions](/concepts/organizations/#roles-and-permissions).
:::

## When the cycle resets

- **Free** cycles are **anniversary-based**: they reset on the same day-of-month your organization was
  created. An org created on **Jan 15** resets on **Feb 15**, **Mar 15**, and so on (clamped for short
  months - e.g. a Jan 31 anniversary lands on Feb 28).
- **Pro** cycles follow your **subscription's billing period** from Polar. The displayed usage, reset
  date, and estimated charge all track that period - so a subscription that renews on the 30th resets on
  the 30th, not on your original org anniversary.

## Free: the hard cap

When a Free org reaches **1,000 credits** in a cycle, further uploads are **blocked** until the next
reset. A banner in the dashboard tells you you've hit the cap. Your test runs are unaffected - they
pass and fail exactly as before; they just aren't recorded until the cycle rolls over (or you upgrade).

## Pro: included + metered overage

Pro includes **10,000 credits** per cycle. Past that:

- Each additional credit is metered at **$0.001** - an early-bird rate, half the standard $0.002, locked
  for at least 2 years for orgs that start Pro now.
- Overage is billed **in arrears** - it appears on your **next** invoice, on top of the $20 base.
- **Pro never blocks.** Uploads keep flowing no matter how far over you go.

The Billing screen shows an **estimated charge this period**, computed from your current usage:

```
estimate = $20 base + $0.001 × max(0, credits − 10,000)
```

| Credits this cycle | Estimated charge |
| ------------------ | ---------------- |
| 8,000              | $20.00           |
| 10,000             | $20.00           |
| 15,000             | $25.00           |
| 35,000             | $45.00           |

:::note
The on-screen figure is an **estimate** from Nijam's own counter. Your actual invoice is metered and
issued by Polar, which is the source of truth if the two ever differ.
:::

## Managing payment & invoices

Checkout, the customer portal, payment methods, and invoice downloads are handled by
[Polar](https://polar.sh). Reach them from the **Billing** screen → **Manage subscription**.
