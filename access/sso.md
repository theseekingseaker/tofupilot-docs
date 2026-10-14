<!-- Source: https://www.tofupilot.com/docs/access/sso -->
# Single Sign-On
URL: /docs/access/sso

Learn how to authenticate every TofuPilot member through your identity provider with SAML 2.0 or OIDC, so credentials never live on TofuPilot.

***

title: Single Sign-On
description: "Learn how to authenticate every TofuPilot member through your identity provider with SAML 2.0 or OIDC, so credentials never live on TofuPilot."
-------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Step, Steps } from 'fumadocs-ui/components/steps';
import { Callout } from 'fumadocs-ui/components/callout';
import { Cards, Card } from 'fumadocs-ui/components/card';
import { ArrowLeftRight, Shield } from 'lucide-react';

Single sign-on (SSO) delegates authentication to your identity provider (Okta, Microsoft Entra, Azure AD, Auth0, OneLogin, Google Workspace). Anyone whose email matches your domain is redirected to your provider on every login, so credentials never live on TofuPilot.

SSO covers authentication only. For directory-driven membership and role assignment, add [SCIM provisioning](/docs/access/scim). Configuring SSO requires the Owner role.

<Callout type="info">
  SSO is available on Enterprise plans. [Contact sales](https://www.tofupilot.com/contact).
</Callout>

## Protocols

| Protocol     | Notes                                                           |
| ------------ | --------------------------------------------------------------- |
| **OIDC**     | JSON-based. Default for Okta, Auth0, Microsoft Entra, OneLogin. |
| **SAML 2.0** | XML-based. Used in legacy and government environments.          |

## Configure OIDC

OIDC requires four values: issuer URL, client ID, client secret, and redirect URI. You collect them from your IdP and paste them into TofuPilot.

<Steps>
  <Step>
    In your IdP, create an OIDC application for TofuPilot with application type **Web**, then grant access to the users or groups you want signing in.
  </Step>

  <Step>
    In TofuPilot, open **Settings > Organization > Single Sign-On** and pick the **OIDC** tab.
  </Step>

  <Step>
    Fill in the form:

    * **Email Domain**: your domain, e.g. `acme.com`.
    * **Issuer URL**: from your IdP application.
    * **Client ID** and **Client Secret**: from your IdP application.
  </Step>

  <Step>
    Copy the **Callback URL** shown in the form, then paste it into your IdP application's allowed redirect URIs.
  </Step>

  <Step>
    Save.
  </Step>
</Steps>

TofuPilot validates the connection by fetching your IdP's discovery document.

### Required OIDC scopes

Your OIDC application has to grant these scopes so TofuPilot can read the identity claims it needs:

* `openid`
* `profile`
* `email`

## Configure SAML 2.0

SAML uses an XML metadata exchange between TofuPilot (the service provider) and your IdP, so the setup is a two-way swap of identifiers and certificates.

<Steps>
  <Step>
    In your IdP, create a SAML application for TofuPilot.
  </Step>

  <Step>
    In TofuPilot, open **Settings > Organization > Single Sign-On** and pick the **SAML 2.0** tab.
  </Step>

  <Step>
    Fill in the form:

    * **Email Domain**: your domain.
    * **IdP SSO URL**: your provider's SSO endpoint.
    * **IdP Certificate**: X.509 signing certificate in PEM format.
  </Step>

  <Step>
    Copy the **SP Metadata URL** and import it into your IdP, or configure manually with the **ACS URL** and **Entity ID** shown.
  </Step>

  <Step>
    Save.
  </Step>
</Steps>

### Required SAML attribute mappings

Your SAML assertion has to include at least the email attribute, and a display name is recommended.

| TofuPilot attribute     | Required | Source                |
| ----------------------- | -------- | --------------------- |
| `email`                 | Yes      | Primary email address |
| `name` or `displayName` | No       | Full display name     |

## Sessions

A successful SSO sign-in creates a 24-hour session. After expiry, you are redirected to your IdP again, because there is no TofuPilot password to fall back on.

## Break-glass account

Keep one Owner account that signs in with email, not SSO. You use it if the IdP goes down or SSO breaks.

<Callout type="warn">
  Verify the break-glass account before enabling SSO enforcement.
</Callout>

## Enforce SSO

<Steps>
  <Step>
    Open **Settings > Organization > Single Sign-On**.
  </Step>

  <Step>
    Toggle **Require SSO for all members**.
  </Step>
</Steps>

Members who try email/password are bounced to the IdP. The break-glass account is exempt.

## Troubleshooting

| Symptom                                         | Cause                                                                      |
| ----------------------------------------------- | -------------------------------------------------------------------------- |
| Users not redirected to IdP                     | Email domain mismatch (subdomains count). Configuration not saved.         |
| IdP login succeeds but TofuPilot errors         | Callback URL wrong, required scopes missing, `email` attribute not mapped. |
| IdP group changes do not affect TofuPilot roles | SSO does not push groups. Use [SCIM](/docs/access/scim).                   |

<Cards>
  <Card icon={<ArrowLeftRight />} title="SCIM provisioning" href="/docs/access/scim" description="Provision, update, and deactivate members automatically from your directory." />

  <Card icon={<Shield />} title="Two-factor authentication" href="/docs/access/two-factor-authentication" description="Require a passkey or authenticator app on every sign-in." />
</Cards>
