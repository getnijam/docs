---
title: Okta SSO setup
description: Step-by-step Okta configuration for Nijam SSO, including the Okta-specific gotchas - app type, issuer URL, Federation Broker Mode, and the dashboard chiclet.
sidebar:
  order: 3
---

This is the Okta-specific walkthrough for [single sign-on](/account/sso/) - the exact screens plus the
handful of Okta quirks that trip people up. For the general concepts (JIT provisioning, enforcement,
domain verification), see the [main SSO page](/account/sso/).

:::note
Configuring SSO is **admin-only** and requires your org to be on **Pro**.
:::

## 1. Create the OIDC app

In the **Okta Admin Console**: **Applications → Create App Integration**.

1. Choose **OIDC - OpenID Connect**, then **Web Application**.
   - :::caution
     Pick **Web Application** - a confidential, server-side client that gets a **client secret**. Do
     **not** pick *Single-Page Application* or *Native* - those are public clients with no secret, and
     Nijam needs the secret.
     :::
2. **Grant type:** leave **Authorization Code** checked. You can leave **Refresh Token** unchecked -
   Nijam reads your profile once and mints its own session, so it never needs to refresh Okta tokens.
   Leave **DPoP** unchecked.
3. **Sign-in redirect URIs:** paste the **Redirect URI** shown on Nijam's SSO settings page -
   `https://api.nijam.dev/v1/auth/sso/callback`.
4. **Sign-out redirect URIs:** leave blank (Nijam doesn't use OIDC sign-out).
5. **Trusted Origins → Base URIs:** leave blank - that field is only for self-hosting Okta's Sign-In
   Widget, which Nijam doesn't use.
6. Save, then copy the **Client ID** and **Client secret**.

## 2. Find your Issuer URL

Your issuer is your Okta **org domain**. The easiest way to find it: in the Okta Admin Console, click
your **username in the top-right corner** - your domain (e.g. `https://dev-123456.okta.com`) is listed
in the dropdown. Paste that as the **Issuer URL**.

Confirm it by opening `https://dev-123456.okta.com/.well-known/openid-configuration` - it should return
JSON. A wrong issuer is the most common cause of a "we couldn't reach that identity provider" error.

## 3. Connect it in Nijam

Open **Org settings → Single sign-on → Connect provider** and paste the **Issuer URL**, **Client ID**,
and **Client secret**. Then verify your company domain under **Org settings → Domains** (add it, publish
the DNS TXT record Nijam shows, and click **Verify**). SSO only routes a login once a domain is
**Verified**.

## 4. Assign users - the Federation Broker Mode gotcha

If you try to assign users and Okta says:

> It is not possible to assign users to an AppInstance that has Federation Broker Mode enabled

…then **Federation Broker Mode (FBM)** is on. FBM lets *any* user in your Okta org use the app without
explicit assignment - but the trade-off is you **can't assign individual users** and the app **won't
appear as a dashboard tile**.

To assign users and show the tile, turn FBM off:

1. **Applications → your app → General → General Settings → Edit.**
2. Find **Federation Broker Mode** and turn it **off**. Save.
3. Go to the **Assignments** tab and assign your users/groups.

:::note
- With FBM **off**, assignment is enforced - **assign yourself (or "Everyone")** before testing, or
  you'll be blocked at Okta.
- The email-first SSO login (starting from Nijam) works either way - FBM only affects user assignment
  and the dashboard tile, not the sign-in flow itself.
:::

## 5. Add Nijam to the Okta dashboard (the chiclet)

To let people launch Nijam from their Okta dashboard, point Okta's **Initiate login URI** at Nijam's
**launch link**.

1. Copy the **Launch link** from **Org settings → Single sign-on** (e.g.
   `https://api.nijam.dev/v1/auth/sso/launch/<connection-id>`).
2. In the Okta app: **General → LOGIN** section → set:
   - **Login initiated by:** App and Okta sign-on page.
   - **Initiate login URI:** the launch link.
   - **Application visibility:** check **Display application icon to users**.
3. Optionally upload the Nijam logo so the chiclet looks right.

:::caution
The **Initiate login URI must be HTTPS** and reachable from the user's browser. Use the launch link
exactly as shown on Nijam's SSO settings page
(`https://api.nijam.dev/v1/auth/sso/launch/<connection-id>`).
:::

Assigned users now see a **Nijam** tile on their Okta dashboard. Clicking it sends them through your
Initiate login URI → Okta → back into Nijam, signed in.

## Troubleshooting (Okta)

| What you see | Fix |
| --- | --- |
| "It is not possible to assign users… Federation Broker Mode enabled" | Turn **Federation Broker Mode** off (step 4), then assign users. |
| Clicking the chiclet shows a `NOT_FOUND` JSON error | The launch link points at an API build that doesn't have the route yet - redeploy your API, then retry. |
| "We couldn't reach that identity provider" | Wrong issuer URL - you likely used the `-admin` console URL. Use the org base URL and confirm its `/.well-known/openid-configuration` loads. |
| A redirect/`redirect_uri` mismatch at Okta | The **Sign-in redirect URI** in Okta must exactly match Nijam's (`https://api.nijam.dev/v1/auth/sso/callback`). |
| Okta rejects the Initiate login URI | It must be **HTTPS** and a real, reachable host. Use the production launch link from Nijam's SSO settings. |
| Enforced user can't sign in at all | Okta is unavailable or they're unassigned. An admin can lift **Require SSO** / **Enabled** in Nijam to restore password login. |
