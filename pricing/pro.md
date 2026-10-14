<!-- Source: https://www.tofupilot.com/docs/pricing/pro -->
# Pro Plan
URL: /docs/pricing/pro

Learn how the TofuPilot Pro plan works at $50 per user per month, with on-demand overage on runs, attachment storage, and stations.

***

title: Pro Plan
description: "Learn how the TofuPilot Pro plan works at $50 per user per month, with on-demand overage on runs, attachment storage, and stations."
--------------------------------------------------------------------------------------------------------------------------------------------------

import { Callout } from 'fumadocs-ui/components/callout';
import { Cards, Card } from 'fumadocs-ui/components/card';
import { FlaskConical, Building, CreditCard } from 'lucide-react';

The Pro plan is billed per user per month and ships with included quotas on runs, storage, and stations. Once you exceed a quota, usage is billed on-demand at the rates below.

## What's included

Pro gives you the headroom most teams need to run a production line on TofuPilot Cloud.

| Resource           | Included        | Overage               |
| ------------------ | --------------- | --------------------- |
| Seats              | 1               | $50 / seat / month    |
| Test runs          | 1,000 / month   | $0.01 / run           |
| Attachment storage | 100 GB / month  | $0.10 / GB            |
| Stations           | 1               | $50 / station / month |
| Hosting            | TofuPilot Cloud |                       |
| Support            | Email           |                       |

## Per-user pricing

Pro is $50 per user per month, and every member consumes one seat regardless of role.

| Role      | Counts toward seat billing |
| --------- | -------------------------- |
| Owner     | Yes                        |
| Admin     | Yes                        |
| Developer | Yes                        |
| Viewer    | Yes                        |
| Operator  | Yes                        |

Banned users do not count, and removed users stop billing on the next cycle.

## Test runs

Pro includes 1,000 runs per month, and a run is counted on upload. Runs that sit in the [offline queue](/docs/cli/queue) do not count until they upload.

Overage is billed at $0.01 per run with no rate cap.

[Reference Samples](/docs/fundamentals/device-data-model#unit) flagged `golden` or `failing` count the same as production runs.

## Attachment storage

Pro includes 100 GB of attachment storage per month. Attachments are files that [Phases](/docs/fundamentals/test-data-model#phase) upload alongside a run.

Storage is measured at the end of each billing cycle, and overage is billed at $0.10 per GB per month. To free space, delete attachments under **Runs > Attachments**.

<Callout type="info">
  Large attachments are the most common source of overage. Downsample waveforms or attach only on failure.
</Callout>

## Stations

Pro includes one station, and additional stations are $50 per station per month.

Partial-month stations are prorated, and removing a station mid-cycle stops billing the next day.

## Beyond Lab

Compared to Lab, Pro unlocks access groups and other access features.

* Multiple seats, so you can invite teammates and assign [roles](/docs/access/access-roles).
* Multiple stations.
* [Access Groups](/docs/access/groups).
* [SSO](/docs/access/sso) (SAML 2.0 or OIDC).
* 2FA enforcement.
* Email support.

## Not on Pro

A few capabilities stay Enterprise-only.

* Self-hosting.
* Air-gapped activation.
* SLA-backed support.

For more detail, see [Enterprise](/docs/pricing/enterprise).

## Usage and notifications

Open **Settings > Usage** to see the current month run count, storage, and per-station breakdown.

Email notifications fire at key thresholds so you are not surprised at cycle close.

* 75% of the monthly run allowance.
* 100% of the monthly run allowance. On-demand billing activates.
* Daily and weekly summaries while on-demand billing is active.

## Downgrading to Lab

To move back to Lab, open **Settings > Subscription > Downgrade**. The change takes effect at the end of the current billing cycle.

Every member except the Owner is removed, every station past the first is unlinked, and runs past 100 per month start failing on the next cycle. Your data stays in place.

<Cards>
  <Card icon={<FlaskConical />} title="Lab plan" href="/docs/pricing/lab" description="Free tier and its limits." />

  <Card icon={<Building />} title="Enterprise plan" href="/docs/pricing/enterprise" description="SSO, self-hosting, and custom limits." />

  <Card icon={<CreditCard />} title="Billing" href="/docs/pricing/billing" description="Manage subscription, payment, and invoices." />
</Cards>
