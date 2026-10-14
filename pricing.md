<!-- Source: https://www.tofupilot.com/docs/pricing -->
# Pricing
URL: /docs/pricing

Learn how TofuPilot pricing works across three plans: Lab for free solo use, Pro for growing teams, and Enterprise for scale and compliance.

***

title: Pricing
description: "Learn how TofuPilot pricing works across three plans: Lab for free solo use, Pro for growing teams, and Enterprise for scale and compliance."
-----------------------------------------------------------------------------------------------------------------------------------------------------------

import { Cards, Card } from 'fumadocs-ui/components/card';
import { CreditCard, FlaskConical, Rocket, Building, Receipt, ChartPie, Key } from 'lucide-react';

TofuPilot offers three plans so you can start free, scale up as your team grows, and move to a custom contract once you need self-hosting or SLA-backed support.

| Plan                                       | Best for                                                                             | Starts at          |
| ------------------------------------------ | ------------------------------------------------------------------------------------ | ------------------ |
| **[Lab](/docs/pricing/lab)**               | Solo developers, prototypes, and early-stage startups.                               | Free               |
| **[Pro](/docs/pricing/pro)**               | Growing teams running production.                                                    | $50 / user / month |
| **[Enterprise](/docs/pricing/enterprise)** | Larger organizations needing SSO, self-hosting, custom limits, or dedicated support. | Contact sales      |

You can find the live feature matrix at [tofupilot.com/pricing](https://www.tofupilot.com/pricing).

## Lab

The Lab plan is free forever and covers solo development. It includes one seat, one [Station](/docs/station), 100 monthly test runs, and 10 GB of attachment storage. You get the full [framework](/docs/frameworks) catalog, [CLI](/docs/cli), [analytics](/docs/run-analytics), and the [REST API](/docs/api/rest-api).

For the full breakdown, see the [Lab plan](/docs/pricing/lab).

## Pro

The Pro plan is billed per user per month, with on-demand overage on runs, storage, and stations once you exceed the included quotas.

| Resource           | Included       | Overage               |
| ------------------ | -------------- | --------------------- |
| Seats              | 1              | $50 / seat / month    |
| Test runs          | 1,000 / month  | $0.01 / run           |
| Attachment storage | 100 GB / month | $0.10 / GB            |
| Stations           | 1              | $50 / station / month |

Pro also unlocks email support, [access group management](/docs/access/groups), and [SSO](/docs/access/sso).

For more detail, see the [Pro plan](/docs/pricing/pro).

## Enterprise

The Enterprise plan is a custom annual contract that covers SSO and SCIM at scale, self-hosting, air-gapped activation, custom run and storage allowances, SLA-backed support, and MES integration.

For more detail, see the [Enterprise plan](/docs/pricing/enterprise).

## Subscription

You manage your subscription under **Settings > Subscription**, where you can upgrade, downgrade, update payment, and pull invoices. Billing runs through Stripe.

For the full workflow, see [Billing](/docs/pricing/billing).

## Usage and overages

Open **Settings > Usage** to see your current month run count, storage, and per-station and per-seat usage.

Pro switches to on-demand billing once you exceed the included quotas, and email notifications fire at 75% and 100%. Enterprise overage handling is negotiated at contract time.

<Cards>
  <Card icon={<CreditCard />} title="Billing" href="/docs/pricing/billing" description="Manage subscription, payment, and invoices." />

  <Card icon={<FlaskConical />} title="Lab plan" href="/docs/pricing/lab" description="Free tier limits." />

  <Card icon={<Rocket />} title="Pro plan" href="/docs/pricing/pro" description="Per-user pricing and overages." />

  <Card icon={<Building />} title="Enterprise plan" href="/docs/pricing/enterprise" description="SSO, self-hosting, and custom limits." />

  <Card icon={<Receipt />} title="Invoices" href="/docs/pricing/invoices" description="Stripe invoices." />

  <Card icon={<ChartPie />} title="Usage" href="/docs/pricing/usage" description="Per-cycle quota tracking." />

  <Card icon={<Key />} title="License" href="/docs/pricing/license" description="Self-hosted license JWT." />
</Cards>
