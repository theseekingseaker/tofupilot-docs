<!-- Source: https://www.tofupilot.com/docs/pricing/enterprise -->
# Enterprise Plan
URL: /docs/pricing/enterprise

Learn how the TofuPilot Enterprise plan covers SSO, self-hosting, air-gapped activation, and SLA-backed support for larger organizations.

***

title: Enterprise Plan
description: "Learn how the TofuPilot Enterprise plan covers SSO, self-hosting, air-gapped activation, and SLA-backed support for larger organizations."
--------------------------------------------------------------------------------------------------------------------------------------------------------

import { Cards, Card } from 'fumadocs-ui/components/card';
import { Rocket, Server, LogIn } from 'lucide-react';

The Enterprise plan is a custom annual contract for organizations that need self-hosting, SSO at scale, or scale past the Pro limits. To get started, [contact sales](https://www.tofupilot.com/contact).

## Beyond Pro

Enterprise includes everything in Pro and adds the capabilities you need to run TofuPilot under your own infrastructure and compliance rules.

| Capability                                                                   | Detail                                                             |
| ---------------------------------------------------------------------------- | ------------------------------------------------------------------ |
| Custom Run allowance                                                         | Negotiated at contract time, with no per-run on-demand charge.     |
| Custom storage allowance                                                     | Negotiated at contract time.                                       |
| Custom seat and Station pricing                                              | Volume discounts.                                                  |
| [Self-hosting](/docs/self-hosting)                                           | Run TofuPilot on your own Docker host or Kubernetes cluster.       |
| [Air-gapped activation](/docs/self-hosting/operations#air-gapped-activation) | Deploy in isolated networks with no external egress.               |
| [SCIM provisioning](/docs/access/scim)                                       | Directory-driven member and access group management.               |
| Dedicated success manager                                                    | Named contact.                                                     |
| SLA-backed support                                                           | Committed response times for production-impacting issues.          |
| MES integration support                                                      | Engineering help wiring TofuPilot into your MES or ERP.            |
| Audit Log retention                                                          | Up to 1 year on the [API Audit Log](/docs/security/api-audit-log). |

## Self-hosting

Enterprise self-hosting comes in two deployment modes so you can match your network policy.

* **Connected**: the self-hosted instance talks to the licensing endpoint for activation checks. You use this mode when egress is allowed.
* **Air-gapped**: there is no external egress. Activation tokens are minted on the dashboard and pasted into the self-hosted instance manually.

For the full deployment guide, see [Self-hosting](/docs/self-hosting).

## Security and compliance

Enterprise covers the controls you need for organization-wide access and audit.

* SSO/SAML or OIDC through every supported provider (Okta, Microsoft Entra, Auth0, OneLogin, Google Workspace, Keycloak, JumpCloud, and custom OIDC).
* SCIM provisioning with group-to-role mapping.
* Two-factor enforcement organization-wide.
* API Audit Log with extended retention.
* Role-based access scoped per access group and per station.
* Data residency: self-hosting keeps data in your network, and Cloud Enterprise instances can be pinned to a region.

For certifications (SOC 2, ISO 27001, HIPAA BAA), raise the question during sales. The answers depend on your deployment mode.

## Support

Enterprise support is structured around production-grade engagements.

* Dedicated success manager.
* Committed first-response SLA on production-impacting issues.
* Pre-scheduled review calls and roadmap input.
* Engineering help on procedure migration and MES integration during onboarding.

Support runs through email and a private Slack or Teams channel.

<Cards>
  <Card icon={<Rocket />} title="Pro plan" href="/docs/pricing/pro" description="Per-user pricing and overages." />

  <Card icon={<Server />} title="Self-hosting" href="/docs/self-hosting" description="Run TofuPilot on your own infrastructure." />

  <Card icon={<LogIn />} title="Single sign-on" href="/docs/access/sso" description="SAML 2.0 and OIDC configuration." />
</Cards>
