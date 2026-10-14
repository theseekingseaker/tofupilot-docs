<!-- Source: https://www.tofupilot.com/docs/pricing/billing -->
# Billing
URL: /docs/pricing/billing

Learn how to manage your TofuPilot subscription, update your payment method, and download invoices through the Stripe customer portal.

***

title: Billing
description: "Learn how to manage your TofuPilot subscription, update your payment method, and download invoices through the Stripe customer portal."
-----------------------------------------------------------------------------------------------------------------------------------------------------

import { Step, Steps } from 'fumadocs-ui/components/steps';
import { Callout } from 'fumadocs-ui/components/callout';
import { Cards, Card } from 'fumadocs-ui/components/card';
import { CreditCard, FlaskConical, Rocket, Building } from 'lucide-react';

TofuPilot bills through Stripe, so card numbers never touch our infrastructure. For what each plan includes, see [Lab](/docs/pricing/lab), [Pro](/docs/pricing/pro), and [Enterprise](/docs/pricing/enterprise).

## Where to find your subscription

Each organization carries its own subscription, and you reach it from the user menu.

<Steps>
  <Step>Open the user menu in the bottom-left.</Step>
  <Step>Select **Settings**.</Step>
  <Step>Open the **Subscription** tab.</Step>
</Steps>

You will see the current plan, billing cycle, payment method, and a link to the Stripe customer portal.

## Upgrade

You upgrade your plan from the Subscription tab, and Lab to Pro is self-serve while Pro to Enterprise routes through sales.

<Steps>
  <Step>Open **Settings > Subscription**.</Step>
  <Step>Click **Upgrade**. Lab to Pro is self-serve, and Pro to Enterprise opens a sales contact form.</Step>
  <Step>Enter your payment details in Stripe checkout. Existing cards stay on file.</Step>
  <Step>The plan switches immediately.</Step>
</Steps>

The first Pro invoice covers the partial month from the upgrade date, prorated.

## Update payment method

You manage payment details through the Stripe portal linked from your subscription.

<Steps>
  <Step>Open **Settings > Subscription > Manage subscription**.</Step>
  <Step>You are redirected to Stripe. From there, you can add or replace a card, change the billing address, or update the company name and tax ID.</Step>
  <Step>Changes apply immediately.</Step>
</Steps>

## Monthly vs annual

Pro defaults to monthly billing. To switch to annual (discounted), email [support@tofupilot.com](mailto:support@tofupilot.com).

Enterprise is annual by default.

## Download invoices

You can pull invoice PDFs straight from the Stripe portal.

<Steps>
  <Step>Open **Settings > Subscription > Manage subscription**.</Step>
  <Step>In the Stripe portal, open **Invoices**.</Step>
  <Step>Click an invoice to download the PDF, or **Resend** to mail it.</Step>
</Steps>

Invoices break out seat, station, run overage, and storage overage, and the line items match what you see on **Settings > Usage**.

## Billing email

The billing email is the address Stripe uses for receipts and dunning, and it does not need to match an organization member.

<Steps>
  <Step>Open the Stripe portal from **Settings > Subscription > Manage subscription**.</Step>
  <Step>Update the **Email** field on the customer profile.</Step>
  <Step>Save.</Step>
</Steps>

## Cancel or pause

You can cancel or pause your subscription from the Stripe portal, and the organization stays active through the end of the cycle.

<Steps>
  <Step>Open **Settings > Subscription > Manage subscription**.</Step>
  <Step>Select **Cancel plan** or **Pause plan**.</Step>
  <Step>Confirm. The organization stays active through the end of the cycle.</Step>
</Steps>

After the cycle ends, the organization drops to Lab and all data is preserved.

<Callout type="warn">
  Canceling does not delete data. To remove your organization, delete the organization separately under **Settings > Organization > Delete organization**.
</Callout>

## Failed payments

If a charge fails, Stripe retries it over the next few days. While the subscription is `past_due`:

* The dashboard shows a banner with the next retry date.
* The organization keeps full access.
* Stripe emails the billing address.

If every retry fails, the subscription moves to `unpaid` and access drops to read-only until you restore payment.

## Tax and VAT

Stripe calculates tax from the billing address. EU customers can add a VAT ID for reverse-charge invoicing, and US customers in tax states see sales tax on every invoice.

For exemption (universities, government, non-profits), email [support@tofupilot.com](mailto:support@tofupilot.com) with your certificate.

## Questions

For quotes, contract terms, custom add-ons, or refunds, reach out at [support@tofupilot.com](mailto:support@tofupilot.com).

<Cards>
  <Card icon={<CreditCard />} title="Pricing" href="/docs/pricing" description="Plans overview." />

  <Card icon={<FlaskConical />} title="Lab plan" href="/docs/pricing/lab" description="Free tier." />

  <Card icon={<Rocket />} title="Pro plan" href="/docs/pricing/pro" description="Per-user pricing." />

  <Card icon={<Building />} title="Enterprise plan" href="/docs/pricing/enterprise" description="Custom contracts." />
</Cards>
