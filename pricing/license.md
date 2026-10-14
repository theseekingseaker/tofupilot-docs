<!-- Source: https://www.tofupilot.com/docs/pricing/license -->
# License
URL: /docs/pricing/license

Learn how the TofuPilot license JWT works on self-hosted instances, including delivery modes, contents, expiry behavior, and refresh failures.

***

title: License
description: "Learn how the TofuPilot license JWT works on self-hosted instances, including delivery modes, contents, expiry behavior, and refresh failures."
-------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Callout } from 'fumadocs-ui/components/callout';
import { Cards, Card } from 'fumadocs-ui/components/card';
import { Server, Building, ChartPie } from 'lucide-react';

A TofuPilot license is a signed JWT issued by [Orbit](https://www.tofupilot.com/orbit) that authorizes a [self-hosted](/docs/self-hosting) instance. It encodes the plan tier, hard limits (seats, stations, runs per month), enabled features (SSO, SCIM, Access Groups, MCP server), and expiry, and the instance verifies it on every request against an embedded Ed25519 public key so no network call is needed once it is stored.

Licenses apply to self-hosted only, and Cloud customers are governed by their Stripe [subscription](/docs/pricing/billing).

## Delivery

Licenses can reach your instance in two modes depending on whether outbound HTTPS is allowed.

| Mode           | Trigger                                                                    | Use when                                            |
| -------------- | -------------------------------------------------------------------------- | --------------------------------------------------- |
| **Phone-home** | The instance fetches a fresh JWT from Orbit using `TOFUPILOT_LICENSE_KEY`. | The instance has outbound HTTPS to `tofupilot.com`. |
| **Air-gapped** | An operator copies the JWT from Orbit and pastes it into the dashboard.    | The instance has no internet.                       |

Both modes verify the JWT signature with the same embedded public key.

## Where to find the license

Open **Settings > Plan** to view the license card, which shows the plan tier, expiry, current limits, enabled features, and the instance ID. The usage counts on the same page show how close you are to each limit.

### Refresh from Orbit

To pull a fresh license, click **Refresh license** on the license card. The instance posts the license key and instance ID to Orbit, receives a new JWT, verifies the signature, and stores it.

A failed refresh leaves the existing license in place, so there is no silent downgrade.

### Air-gapped activation

For air-gapped deployments, you transfer the license token manually.

1. In [Orbit](https://www.tofupilot.com/orbit), open the instance menu and select **Copy license token**.
2. In your self-hosted dashboard, open **Settings > Plan**.
3. Click the upload button on the license card.
4. Paste the JWT and click **Activate**.

The instance verifies the signature locally and stores the JWT. For the initial-deploy flow, see [Operations](/docs/self-hosting/operations#air-gapped-activation).

<Callout type="warn">
  Air-gapped instances do not auto-renew. Set a reminder before expiry and rotate manually.
</Callout>

## License contents

The decoded JWT carries the fields the instance reads at runtime.

| Field                   | Meaning                                                                                                                                                                            |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `sub`                   | Instance ID. Must match the deployment.                                                                                                                                            |
| `org`                   | Organization name.                                                                                                                                                                 |
| `plan`                  | `lab`, `pro`, or `enterprise`.                                                                                                                                                     |
| `limits.stations`       | Max active stations, or `null` for unlimited.                                                                                                                                      |
| `limits.users`          | Max active seats, or `null` for unlimited.                                                                                                                                         |
| `limits.runs_per_month` | Monthly run cap, or `null` for unlimited.                                                                                                                                          |
| `features`              | Enabled add-ons (`sso`, `scim`, `teams`, `mcp`). Without `mcp`, the instance has no MCP server: the endpoint answers 403 and **Settings > MCP** shows the feature as not included. |
| `iat`                   | Issued-at timestamp.                                                                                                                                                               |
| `exp`                   | Expiry timestamp.                                                                                                                                                                  |

The instance caches the decoded license in-memory for 60 seconds.

## Expiry behavior

After expiry, the instance walks through escalating warnings rather than cutting off access immediately.

| State              | Days past expiry | Effect                                                                         |
| ------------------ | ---------------- | ------------------------------------------------------------------------------ |
| `valid`            | Not expired      | Normal operation.                                                              |
| `warning`          | 0 to 30          | A banner appears and nothing is restricted.                                    |
| `write_restricted` | 30 to 90         | New users and stations cannot be created, but existing data and runs continue. |
| `nag_modal`        | 90+              | A renewal modal blocks every page.                                             |

To clear the state, you renew in Orbit (or contact sales for Enterprise) and refresh the instance.

## Authentication

The `TOFUPILOT_LICENSE_KEY` is an opaque secret tied to the deployment in Orbit, and you should treat it like an [API key](/docs/access/api-keys) by storing it in your secret manager and never committing it to Git.

The JWT itself is not a secret. It is signed and tamper-evident, and it grants no authority beyond the encoded limits.

If the signing key rotates, existing JWTs become invalid. The deploy script bundles the matching public key per release, so you upgrade to the latest [version](/docs/self-hosting/upgrade) to get the new key.

## Refresh failures

A failed refresh does not change the active license. Common causes:

* **No outbound HTTPS**: the instance is air-gapped, so switch to manual activation.
* **License key revoked in Orbit**: the contract was canceled or replaced, so contact sales.
* **Orbit is down**: retry. The cached license keeps the instance running.

The dashboard surfaces refresh errors on the license card, and service does not interrupt until the license expires.

<Cards>
  <Card icon={<Server />} title="Operations" href="/docs/self-hosting/operations" description="License refresh, backups, and air-gapped activation." />

  <Card icon={<Building />} title="Enterprise plan" href="/docs/pricing/enterprise" description="Self-hosting eligibility and contract terms." />

  <Card icon={<ChartPie />} title="Usage" href="/docs/pricing/usage" description="Track limits embedded in the license." />
</Cards>
