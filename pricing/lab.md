<!-- Source: https://www.tofupilot.com/docs/pricing/lab -->
# Lab Plan
URL: /docs/pricing/lab

Learn how the TofuPilot Lab plan works as a free single-user tier with one station, 100 monthly runs, and full framework and SDK access.

***

title: Lab Plan
description: "Learn how the TofuPilot Lab plan works as a free single-user tier with one station, 100 monthly runs, and full framework and SDK access."
-------------------------------------------------------------------------------------------------------------------------------------------------------

import { Cards, Card } from 'fumadocs-ui/components/card';
import { Rocket, Building } from 'lucide-react';

The Lab plan is the free TofuPilot tier for a single user, and you can use it indefinitely without entering a payment method.

## What's included

Lab gives you enough quota to prototype a procedure end to end on TofuPilot Cloud.

| Resource           | Quota           |
| ------------------ | --------------- |
| Seats              | 1               |
| Stations           | 1               |
| Test runs          | 100 / month     |
| Attachment storage | 1 GB / month    |
| Hosting            | TofuPilot Cloud |
| Support            | Community       |

## What works

Every core capability is available on Lab so you can build a real procedure.

* All four [supported frameworks](/docs/frameworks).
* [CLI](/docs/cli).
* One [Station](/docs/station).
* [Git-driven Deployments](/docs/deployments/git).
* [Analytics](/docs/run-analytics), [yield](/docs/run-analytics/run-analytics), and [process control](/docs/run-analytics/measurement-control).
* The REST API and every [SDK](/docs/api/rest-api).

## What's not available

A few capabilities require a paid plan.

* Multiple seats require Pro or Enterprise.
* SSO and SCIM require Pro or Enterprise.
* More than one Station requires Pro or Enterprise.
* Self-hosting requires Enterprise.
* SLA-backed support requires Enterprise.

## Upgrading

When you outgrow Lab, you can upgrade from **Settings > Subscription** and your data carries over.

<Cards>
  <Card icon={<Rocket />} title="Pro plan" href="/docs/pricing/pro" description="Per-user pricing and usage overages." />

  <Card icon={<Building />} title="Enterprise plan" href="/docs/pricing/enterprise" description="SSO, self-hosting, and custom limits." />
</Cards>
