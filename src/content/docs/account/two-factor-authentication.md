---
title: Two-factor authentication
description: Add a one-time code from an authenticator app as a second step when you sign in to Nijam - and how to recover with backup codes.
sidebar:
  order: 1
---

**Two-factor authentication (2FA)** adds a second step to your sign-in: after your password,
you enter a short-lived code from an authenticator app on your phone. Even if someone learns
your password, they can't get in without that code.

You'll find it under **Account settings → Security → Two-factor authentication**. (Open the
account menu in the bottom-left of the dashboard and choose **Account settings**.)

## Enabling 2FA

1. Go to **Account settings → Security** and click **Enable** next to *Authenticator app*.
2. **Scan the QR code** with an authenticator app - Google Authenticator, 1Password, Authy,
   Microsoft Authenticator, or any TOTP-compatible app. Can't scan? Enter the **setup key**
   shown below the code manually.
3. Your app now shows a 6-digit code that changes every 30 seconds. Type the current code into
   **Verification code** and click **Enable**.
4. Nijam shows your **backup codes** - save them now (see below), then click **I've saved my
   codes**.

That's it. The next time you sign in with your email and password, you'll be asked for a code.

:::note
Nijam uses standard **TOTP** (time-based one-time passwords), so any authenticator app works.
The codes are generated on your device - Nijam never sees your app or your phone.
:::

## Backup codes

When you enable 2FA, Nijam gives you **10 one-time backup codes** (formatted like `A1B2-C3D4`).
Use one to sign in if you ever lose access to your authenticator app.

- **Each code works once.** After you use a code, it's spent.
- **Store them somewhere safe** - a password manager is ideal. Don't keep them only on the
  device that runs your authenticator app.
- They're shown **only once**, at setup. If you lose them, regenerate a new set.

### Regenerating backup codes

On **Security**, click **Backup codes** and confirm. This issues a fresh set of 10 codes and
**invalidates all previous ones** (used or not).

## Signing in with 2FA

1. Enter your email and password as usual.
2. On the next step, enter the **6-digit code** from your authenticator app.
3. Mistyped it? Just re-enter the code - you won't have to type your password again.

Choose **Use a backup code instead** to sign in with one of your saved backup codes - handy when
your phone isn't with you.

## Lost your device

If you can't get a code from your authenticator app, sign in with a **backup code** instead
(choose *Use a backup code instead* on the code step). Once you're back in, head to
**Security** and **regenerate your backup codes**, then set up your authenticator app again on
your new device by disabling and re-enabling 2FA.

If you've also lost your backup codes and can't sign in, contact your organization admins or
Nijam support to recover access.

## Disabling 2FA

On **Security**, click **Disable** next to *Authenticator app* and confirm with your
**password** (or a current authenticator/backup code if your account has no password). This
removes the second step and deletes your stored secret and backup codes.

## Social logins

Two-factor authentication protects sign-in with your **email and password**. If you sign in with
**Google** or **GitHub**, that provider's own account security applies - we recommend keeping 2FA
enabled on your Google/GitHub account too.

## Accounts without a password

If you only sign in with Google or GitHub (no password set), you can still turn on 2FA for an
extra layer of protection. Sensitive actions that normally re-check your password - like
**disabling 2FA** - will ask for a current authenticator or backup code instead.

See [Organizations](/concepts/organizations/) for how members, roles, and invitations work.
