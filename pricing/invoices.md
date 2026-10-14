<!-- Source: https://www.tofupilot.com/docs/pricing/invoices -->
# Invoices
URL: /docs/pricing/invoices

Learn how to find, download, and audit Stripe invoices for your TofuPilot organization, including line items, proration, and tax handling.

***

title: Invoices
description: "Learn how to find, download, and audit Stripe invoices for your TofuPilot organization, including line items, proration, and tax handling."
---------------------------------------------------------------------------------------------------------------------------------------------------------

import { Cards, Card } from 'fumadocs-ui/components/card';
import { CreditCard, ChartPie, Rocket } from 'lucide-react';

A TofuPilot invoice is the Stripe bill for one billing cycle. It breaks out seats, [Stations](/docs/station), run overage, and storage overage, and it is issued at cycle end or on proration and settled against your saved payment method.

Invoices apply to the [Pro](/docs/pricing/pro) plan. [Lab](/docs/pricing/lab) generates none, and [Enterprise](/docs/pricing/enterprise) bills by contract.

## Where to find invoices

You can reach invoices from the dashboard, the Stripe portal, or skip the tab entirely on self-hosted deployments.

### Recent invoices

Open **Settings > Invoices** to see the last twenty invoices with their date, billing period, amount, and status. Click a row to open the Stripe hosted invoice.

### Full history

Open **Settings > Plan > Manage subscription** to enter the Stripe customer portal, where every invoice ever issued is listed.

### Self-hosted

The Invoices tab is hidden on self-hosted deployments because those organizations are billed by Orbit through Enterprise contracts. For more detail, see [License](/docs/pricing/license).

## Download PDF

You can pull an invoice PDF from the Stripe hosted view in three steps.

1. Open **Settings > Invoices**.
2. Click the invoice row to open the Stripe hosted invoice.
3. Use **Download invoice** at the top right.

The PDF includes a line-item breakdown, billing address, tax (or reverse-charge note), and payment status.

## Line items

Each invoice carries the same set of line items, drawn from a mix of recurring charges and metered usage.

| Line item              | Source                                      | Billing                    |
| ---------------------- | ------------------------------------------- | -------------------------- |
| **Seats**              | Active organization members                 | Per-seat, recurring        |
| **Stations**           | Active stations                             | Per-station, recurring     |
| **Test Run overage**   | Runs past the included monthly allowance    | Metered, billed in arrears |
| **Attachment overage** | Storage past the included monthly allowance | Metered, billed in arrears |

Seats and stations bill on the cycle anchor, and run and storage overage are metered against Stripe meter events and totaled at cycle close. The numbers on **Settings > Usage** match the overage lines.

## Proration

Mid-cycle changes are prorated, and metered overage is not.

* Adding a seat or station produces a partial-cycle line item on the next invoice.
* Removing a seat or station produces a negative line item for the remaining cycle credit.
* An upgrade from Lab to Pro means the first invoice covers the partial cycle.
* To switch billing intervals, email [support@tofupilot.com](mailto:support@tofupilot.com).

Metered overage (runs, storage) is not prorated.

## Failed payments

If a charge fails, Stripe retries automatically and the subscription enters `past_due`. The dashboard shows a banner with the next retry date, and you keep full access during retries.

If every retry fails, the subscription moves to `unpaid` and access drops to read-only until you restore payment.

To fix it, open **Settings > Plan > Manage subscription** and update the card. Stripe will charge the outstanding balance immediately.

## Billing email

Invoices go to the billing email on the Stripe customer profile, not to organization members. You can update it in the Stripe portal under **Customer profile > Email**.

## Tax and VAT

Stripe calculates tax from the billing address. EU customers can add a VAT ID for reverse-charge invoicing, and US customers in tax-applicable states see sales tax.

For tax-exempt status, email [support@tofupilot.com](mailto:support@tofupilot.com) with your certificate.

<Cards>
  <Card icon={<CreditCard />} title="Billing" href="/docs/pricing/billing" description="Manage subscription, payment, and cancellation." />

  <Card icon={<ChartPie />} title="Usage" href="/docs/pricing/usage" description="Per-cycle quota tracking." />

  <Card icon={<Rocket />} title="Pro plan" href="/docs/pricing/pro" description="Quotas, overage rates, and included resources." />
</Cards>
