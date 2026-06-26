---
title: Single sign-on (SSO)
description: Let your team sign in with your company identity provider over OIDC - Okta, Microsoft Entra ID, Auth0, or any OIDC provider - with optional enforcement.
sidebar:
  order: 2
---

Single sign-on lets your team sign in to Nijam with your company identity provider instead of a Nijam
password. Nijam works with any **OpenID Connect (OIDC)** provider - Okta, Microsoft Entra ID, Auth0,
Google Workspace, and others - and is configured **per organization**.

:::note
SSO is a **Pro** feature, and configuring it is **admin-only**. Members can sign in via SSO but can't
change its settings.
:::

## How it works

Sign-in is **identity-first**: on the login page a user enters their work email, and if its domain
belongs to an organization with SSO, Nijam sends them to your identity provider and brings them back
signed in. No password field is shown for an SSO domain.

Under the hood, Nijam uses the OIDC **Authorization Code flow with PKCE**. It discovers your provider's
endpoints from its issuer URL (`/.well-known/openid-configuration`), so there's nothing provider-specific
to configure - Okta and Entra ID are set up the same way. Your client secret is **encrypted at rest**,
and an email domain only routes to your provider once you've **DNS-verified** that you own it.

## Before you start

- Your organization is on **Pro**.
- You're an **admin** of the organization.
- You can create an OIDC application in your identity provider.
- You can add a **DNS TXT record** for your email domain.

## 1. Create an OIDC app in your provider

:::tip
Using **Okta**? Follow the [step-by-step Okta guide](/account/sso-okta/) instead - it covers the exact
screens plus Okta-specific gotchas (issuer URL, Federation Broker Mode, the dashboard chiclet).
:::

Create a **web application** - a confidential, server-side client that gets a **client secret** (not a
SPA or native app). Configure it with:

- **Sign-in redirect URI** - the exact value shown on Nijam's SSO settings page:

  ```
  https://api.nijam.dev/v1/auth/sso/callback
  ```

- **Grant type** - Authorization Code.
- **Scopes** - `openid`, `email`, `profile`.
- **Assignments** - assign the users (or groups) who should be able to sign in.

Then copy the **Issuer URL**, **Client ID**, and **Client secret**.

:::note
Your **Issuer URL** is your provider's base URL - for Okta it's your org domain like
`https://dev-123456.okta.com` (find it under your username in the Okta admin console; see the
[Okta guide](/account/sso-okta/)). Confirm any issuer by opening its
`/.well-known/openid-configuration` in a browser; it should return JSON.
:::

## 2. Configure SSO in Nijam

1. Open **Org settings → Single sign-on** and click **Connect provider**.
2. Paste your **Issuer URL**, **Client ID**, and **Client secret**.
3. Choose your provisioning and role options (see below), then click **Connect provider**.

Nijam validates the connection by fetching your provider's OIDC discovery document, so a wrong issuer
URL is caught immediately. The client secret is encrypted at rest and never shown again - when editing
later, leave the secret field blank to keep the stored one.

## 3. Verify your email domain

A login only routes to SSO once Nijam has confirmed your organization owns the domain.

1. Under **Email domains**, add your company domain (e.g. `company.com`). Public domains like
   `gmail.com` or `outlook.com` can't be used.
2. Nijam shows a **DNS TXT record** - a name and a value. Add it at your DNS provider.
3. Click **Verify**. Once the record is found, the domain shows **Verified** and begins routing logins.
   DNS changes can take a few minutes to propagate.

A domain can be claimed by only one organization.

## Just-in-time provisioning

With **just-in-time (JIT) provisioning** on (the default), the first time someone signs in via SSO with
a verified-domain email, Nijam creates their account and adds them to your organization with the
**default role** you choose (Member or Admin). Existing Nijam users are matched by email and simply have
their provider identity linked.

Turn JIT off to require that users already be members - they must be [invited](/concepts/organizations/)
the normal way before they can sign in via SSO.

:::note
With JIT on, your identity provider is effectively the source of truth for membership: removing a member
in Nijam while they're still active in your IdP will **re-add them** on their next sign-in. To revoke
someone's access for good, deprovision them in your identity provider (or remove the domain / disable
SSO). Signing in via SSO authenticates a person's Nijam account but never grants access to organizations
they aren't a member of - so removing someone from one org never affects their access to others.
:::

## Require SSO (enforcement)

Turn on **Require SSO** to make single sign-on the only way in for your domains. While it's enabled,
for any user whose email is on a verified domain, Nijam blocks:

- password sign-in,
- social sign-in (Google / GitHub),
- creating a new account with a password.

:::note
Enforcing SSO **claims your verified domains.** Everyone with one of those emails signs in through your
identity provider across **all of Nijam** - so if someone uses their work email in another organization
too, that login also goes through your IdP, and deprovisioning them in your IdP removes their Nijam
access everywhere. In other words, a work email is a corporate identity your organization controls. A
domain can only be claimed by one organization.
:::

:::caution
**Test SSO end-to-end before you enforce it.** Enforced users can't fall back to a password, so a
broken or misconfigured identity provider can lock them out. As a safeguard, enforcement only applies
while the connection is **active** and the domain is **verified** - disabling SSO (toggle **Enabled**
off) or removing the domain immediately restores password login.
:::

## Signing in

Tell your team to sign in at the normal login page and enter their **work email**.

- If you've **enforced** SSO, they're sent straight to your identity provider - no password prompt.
- If SSO is **optional**, the next screen offers **Continue with SSO** alongside the password field,
  so members who have a Nijam password can still use it.

Returning users keep the same Nijam account (matched by email); their provider identity is linked on
the first SSO login.

## Add Nijam to your provider's dashboard

You can also let people launch Nijam straight from their Okta dashboard, Microsoft **My Apps**, or
Google launcher - without starting at the Nijam login page. Each connection has a **launch link** (shown
under **Org settings → Single sign-on**). Add it to your provider's app tile; a click sends the user to
your identity provider (where they already have a session) and bounces them back into Nijam, signed in.

| Provider | Where to add the launch link |
| --- | --- |
| **Okta** | Your app → **General → Initiate login URI**, and set **Application visibility** to show the icon (upload the Nijam logo so the chiclet looks right). |
| **Microsoft Entra ID** | The enterprise app → **Home page URL**; assign users/groups and make it visible - it then appears in **My Apps** and the Microsoft 365 app launcher. |
| **Google Workspace** | Admin console → add a **custom link** to the app launcher pointing at the launch link. |
| **Anything else** | Use it as a bookmark or an internal "Sign in to Nijam" link. |

The launch link contains the connection id, not a secret - it only says *which* provider to use; the
user still has to authenticate with your identity provider.

## Disabling SSO

- Toggle **Enabled** off to keep the configuration but stop SSO logins (this also lifts enforcement).
- **Remove SSO** deletes the connection and all its domains. Members keep their accounts and can use
  email/password or social login again.

## Troubleshooting

| What you see | Likely cause |
| --- | --- |
| "Single sign-on isn't available for that domain" | The domain isn't verified yet, the connection is disabled, or the org isn't on Pro. |
| A redirect/callback error at your provider | The sign-in redirect URI in your provider doesn't exactly match the one on Nijam's SSO page. |
| "We couldn't reach that identity provider" | The issuer URL is wrong - check that its `/.well-known/openid-configuration` loads. |
| An enforced user can't sign in at all | The IdP is unavailable. An admin can turn **Enabled** (or **Require SSO**) off to restore password login. |
