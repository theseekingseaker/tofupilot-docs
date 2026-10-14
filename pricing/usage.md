<!-- Source: https://www.tofupilot.com/docs/pricing/usage -->
# Usage
URL: /docs/pricing/usage

Learn how to track seats, stations, runs, attachment storage, and units against your TofuPilot plan quotas in the dashboard.

***

title: Usage
description: "Learn how to track seats, stations, runs, attachment storage, and units against your TofuPilot plan quotas in the dashboard."
-------------------------------------------------------------------------------------------------------------------------------------------

import { Callout } from 'fumadocs-ui/components/callout';
import { Cards, Card } from 'fumadocs-ui/components/card';
import { Rocket, Receipt, ScanQrCode } from 'lucide-react';

The Usage page is the per-cycle count of every billable and quota-limited resource on your organization, including seats, [Stations](/docs/station), [Runs](/docs/fundamentals/test-data-model#run), attachment storage, and [Units](/docs/inventory/units). The counts come from the database and from Stripe meter events.

Usage is admin-only, so Owners and Admins see the dashboard while Developers and Viewers do not.

## Where to find usage

You can view usage at the organization level or scoped to a single procedure.

### Org-wide

Open **Settings > Usage** (or **Usage** in the sidebar) to see counts for the current billing cycle with the included quota as a reference line. The cycle dates come from your Stripe subscription.

### Per-procedure

The **Usage** page in any procedure context (for example, **PCB Test > Usage**) filters totals to runs created under that procedure.

## Tracked metrics

Each tracked metric maps to either a billing line or a plan-enforced quota.

| Metric                 | Counted                                                                                          | Lab limit    |
| ---------------------- | ------------------------------------------------------------------------------------------------ | ------------ |
| **Seats**              | Active organization members, excluding banned users.                                             | 1            |
| **Stations**           | Stations not archived.                                                                           | 1            |
| **Test Runs**          | Runs uploaded this cycle.                                                                        | 100 / month  |
| **Attachment storage** | Total bytes of [Attachments](/docs/fundamentals/test-data-model#attachment) uploaded this cycle. | 1 GB / month |
| **Units**              | Total units in the organization (not per-cycle).                                                 | 50 total     |

Runs and storage reset on the cycle anchor, while seats, stations, and units are point-in-time counts.

<Callout type="info">
  Runs in the CLI [offline queue](/docs/cli/queue) do not count until they upload. Runs flagged `golden` or `failing` count the same as production runs.
</Callout>

## How usage is reported

Each metric is reported from a different source depending on how often it changes.

* **Seats and Stations** are read from the database at request time, so they update instantly.
* **Runs and storage** are reported to Stripe as meter events on every run and attachment upload, and Stripe aggregates them at cycle close.
* **Units** are counted across the organization regardless of cycle.

## Quota enforcement

How a quota is enforced depends on your plan.

### Lab

On Lab, hitting a cap blocks new uploads while leaving existing data untouched.

| Metric   | Behavior at the cap                                                                                                 |
| -------- | ------------------------------------------------------------------------------------------------------------------- |
| Units    | The next `units.create` returns `403 Forbidden` with `unit.limit_exceeded`. Updates to existing units stay allowed. |
| Runs     | Run uploads return `403 Forbidden` with `product.limit_exceeded`.                                                   |
| Storage  | Attachment uploads return `403 Forbidden` with `product.limit_exceeded`.                                            |
| Seats    | Invitations fail.                                                                                                   |
| Stations | Registering a second station fails.                                                                                 |

Existing data is unaffected. Only new uploads block.

### Pro

Pro does not block. Every additional run, gigabyte, seat, or station goes on the next invoice at the [Pro overage rate](/docs/pricing/pro), and email notifications fire at 75% and 100% of the included allowance.

### Enterprise

Cloud Enterprise has no metered limits. Self-hosted Enterprise enforces the limits in the [license](/docs/pricing/license) for runs per month, seats, and stations.

## Self-hosted usage

On self-hosted deployments, the usage dashboard reads from your local database and the quotas come from the embedded license. There is no on-demand billing, so exceeding a license limit blocks the operation until you upgrade the license. For more detail, see [License](/docs/pricing/license).

<Cards>
  <Card icon={<Rocket />} title="Pro plan" href="/docs/pricing/pro" description="Per-cycle quotas and overage rates." />

  <Card icon={<Receipt />} title="Invoices" href="/docs/pricing/invoices" description="Line-item breakdown of metered usage." />

  <Card icon={<ScanQrCode />} title="Units" href="/docs/inventory/units" description="Inventory caps on Lab." />
</Cards>
