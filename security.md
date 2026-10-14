<!-- Source: https://www.tofupilot.com/docs/security -->
# Security
URL: /docs/security

Learn how to audit every API request hitting your TofuPilot organization and review the data trail your security and compliance teams will ask for.

***

title: Security
description: "Learn how to audit every API request hitting your TofuPilot organization and review the data trail your security and compliance teams will ask for."
------------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Cards, Card } from 'fumadocs-ui/components/card';
import { Activity } from 'lucide-react';

Security lives in three places across the product. [Access](/docs/access) covers auth (SSO, SCIM, 2FA, API keys, roles, access groups), and [Self-hosting](/docs/self-hosting) covers data residency and air-gap deployments. This section covers the audit trail your security team reads during an incident.

<Cards>
  <Card icon={<Activity />} title="API audit log" href="/docs/security/api-audit-log" description="A signed trail of every API request to your organization, with caller, endpoint, status, latency, and source IP." />
</Cards>
