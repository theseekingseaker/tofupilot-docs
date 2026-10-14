<!-- Source: https://www.tofupilot.com/docs/access/two-factor-authentication -->
# Two-Factor Authentication
URL: /docs/access/two-factor-authentication

Learn how to strengthen TofuPilot sign-in with a second factor, using passkeys or authenticator-app one-time codes, and how to enforce it org-wide.

***

title: Two-Factor Authentication
description: "Learn how to strengthen TofuPilot sign-in with a second factor, using passkeys or authenticator-app one-time codes, and how to enforce it org-wide."
------------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Step, Steps } from 'fumadocs-ui/components/steps';
import { Cards, Card } from 'fumadocs-ui/components/card';
import { LogIn, User } from 'lucide-react';

Two-factor authentication (2FA) adds a second check on top of email or [SSO](/docs/access/sso) login. Every member manages their own factors; enforcing 2FA organization-wide requires the Owner role.

Two factor types are supported, and either one alone is enough:

* **Passkeys**: WebAuthn backed by laptop biometrics, phone, or hardware key (YubiKey).
* **Authenticator apps**: TOTP from 1Password, Google Authenticator, Authy.

## Enable 2FA

<Steps>
  <Step>
    Open **Settings > Authentication**.
  </Step>

  <Step>
    Click **Enable** in the **Two-factor authentication** card.
  </Step>

  <Step>
    Pick a factor type.
  </Step>

  <Step>
    Save the recovery codes offline.
  </Step>
</Steps>

## Set up a passkey

You can register more than one passkey for the same account.

<Steps>
  <Step>
    Click **Add passkey** under **Settings > Authentication**.
  </Step>

  <Step>
    Pick a credential source: current device, phone via QR, or hardware key.
  </Step>

  <Step>
    Follow the system prompt (Touch ID, Windows Hello, YubiKey tap).
  </Step>

  <Step>
    Name the passkey (`MacBook Pro`, `YubiKey 5`, `iPhone 15`) so you can revoke it later.
  </Step>
</Steps>

## Set up an authenticator app

<Steps>
  <Step>
    Click **Add authenticator app** under **Settings > Authentication**.
  </Step>

  <Step>
    Scan the QR code, or paste the secret manually.
  </Step>

  <Step>
    Enter the six-digit code to confirm pairing.
  </Step>
</Steps>

Every sign-in prompts for a fresh code.

## Recovery codes

Recovery codes bypass 2FA, and each code is single-use.

<Steps>
  <Step>
    Open **Settings > Authentication > Two-factor authentication**.
  </Step>

  <Step>
    Click **Generate new recovery codes**.
  </Step>

  <Step>
    Save the new codes offline.
  </Step>
</Steps>

Generating a new set invalidates the old codes.

## Enforce 2FA organization-wide

<Steps>
  <Step>
    Open **Settings > Organization > Security**.
  </Step>

  <Step>
    Toggle **Require two-factor authentication for all members**.
  </Step>
</Steps>

Members without 2FA enroll on their next sign-in before they can access data. [SSO](/docs/access/sso) members satisfy enforcement when their IdP requires 2FA upstream, because the check reads the SSO assertion.

## Lose your second factor

Owners reset a member's 2FA under **Settings > Members > Reset 2FA**, and the member re-enrolls on their next sign-in.

If you are the only Owner and locked out, contact [support@tofupilot.com](mailto:support@tofupilot.com).

<Cards>
  <Card icon={<LogIn />} title="Single sign-on" href="/docs/access/sso" description="Authenticate members with SAML 2.0 or OIDC through your identity provider." />

  <Card icon={<User />} title="Managing members" href="/docs/access/managing-members" description="Invite members, change roles, and remove members without losing their test data." />
</Cards>
