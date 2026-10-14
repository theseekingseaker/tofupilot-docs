<!-- Source: https://www.tofupilot.com/docs/access/scim -->
# SCIM Provisioning
URL: /docs/access/scim

Learn how to sync members and groups from your identity provider to TofuPilot with SCIM, so account lifecycle stays in your directory.

***

title: SCIM Provisioning
description: "Learn how to sync members and groups from your identity provider to TofuPilot with SCIM, so account lifecycle stays in your directory."
-----------------------------------------------------------------------------------------------------------------------------------------------------

import { Steps, Step } from 'fumadocs-ui/components/steps';
import { Cards, Card } from 'fumadocs-ui/components/card';
import { LogIn, UserCheck } from 'lucide-react';

SCIM provisioning pushes member changes from your identity provider to TofuPilot: add a user to a linked group in Okta and they get a TofuPilot account, remove them and their access is revoked.

SCIM runs on top of [SSO](/docs/access/sso): SSO handles authentication, SCIM handles lifecycle. SSO works without SCIM, but SCIM requires SSO. Configuring SCIM requires the Owner role.

## What SCIM syncs

| Event in your directory               | Effect in TofuPilot                                |
| ------------------------------------- | -------------------------------------------------- |
| User created in linked group          | New member, default role from group mapping        |
| User removed from group               | Member removed from organization                   |
| User attributes updated (name, email) | Profile updated                                    |
| User suspended                        | Sign-in blocked until reactivated in the directory |
| Group renamed or remapped             | Access group membership updated                    |

Role assignment comes from group mapping, so you can map directory group `Engineering` to role `Developer` and group `Operators` to role `Operator`.

## Configure SCIM

Set up [SSO](/docs/access/sso) first.

<Steps>
  <Step>
    Open **Settings > Organization > SCIM Provisioning**.
  </Step>

  <Step>
    Click **Generate token** and copy the bearer token and base URL.
  </Step>

  <Step>
    In your identity provider, create a SCIM 2.0 provisioning connection for TofuPilot, then paste the base URL and bearer token.
  </Step>

  <Step>
    Map directory groups to TofuPilot roles.
  </Step>

  <Step>
    Enable provisioning in your identity provider.
  </Step>
</Steps>

The first sync runs immediately, and subsequent syncs are incremental.

## Verify the sync

Add a test user to a mapped group: the user appears in **Settings > Members** within a minute with the role mapped to their group. Remove the test user from all mapped groups: their membership is removed within a minute.

## Rotate the SCIM token

<Steps>
  <Step>
    Open **Settings > Organization > SCIM Provisioning**.
  </Step>

  <Step>
    Click **Rotate token**.
  </Step>

  <Step>
    Paste the new token into your identity provider's SCIM connection.
  </Step>
</Steps>

The old token is revoked as soon as the new one is generated.

## Limitations

SCIM controls organization membership and roles, but it does not control access group membership. For access group assignment, use the [access groups](/docs/access/groups) UI or the API.

The Owner role cannot be assigned via SCIM, so you have to transfer ownership manually in **Settings > Organization**.

<Cards>
  <Card icon={<LogIn />} title="Single sign-on" href="/docs/access/sso" description="Authenticate members with SAML 2.0 or OIDC through your identity provider." />

  <Card icon={<UserCheck />} title="Access roles" href="/docs/access/access-roles" description="Choose one of five roles, from Owner to Operator, to set what a member can do." />
</Cards>
