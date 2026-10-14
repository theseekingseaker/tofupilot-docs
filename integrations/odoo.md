<!-- Source: https://www.tofupilot.com/docs/integrations/odoo -->
# Odoo
URL: /docs/integrations/odoo

Learn how to create and update Odoo records and post to chatter from a TofuPilot workflow when a test event fires.

***

title: Odoo
description: "Learn how to create and update Odoo records and post to chatter from a TofuPilot workflow when a test event fires."
---------------------------------------------------------------------------------------------------------------------------------

import { Step, Steps } from 'fumadocs-ui/components/steps';

The Odoo integration pushes test data into your Odoo instance, so production records, quality checks, and chatter messages stay in sync with what happens on the line.

## Connect Odoo

You connect Odoo through an API key tied to an Odoo user, and the connection inherits whatever access rights you grant that user.

<Steps>
  <Step>
    In Odoo, create or pick a user account and grant it access to the models the workflow touches.
  </Step>

  <Step>
    Generate an API key under **Preferences > Account Security > New API Key** while signed in as that user.
  </Step>

  <Step>
    In TofuPilot, open **Settings > Integrations > Odoo** and add a connection with the Odoo URL, database name, login, and API key.
  </Step>

  <Step>
    Click **Test connection** to confirm TofuPilot can reach the instance.
  </Step>
</Steps>

## Actions

The integration exposes three actions that cover the most common workflow patterns for Odoo data.

| Action            | Effect                                                                 |
| ----------------- | ---------------------------------------------------------------------- |
| **Create record** | Create a record on any Odoo model with field values from the workflow. |
| **Update record** | Update an existing record by `id`.                                     |
| **Post message**  | Post a message on the chatter of an Odoo record.                       |

### Create record

Pick a model such as `product.product`, `quality.check`, `mrp.production`, or any custom model, and fill the fields you want to write. Values support `{{variable}}` interpolation.

The created record's `id` is exposed as `{{nodeId.id}}`, so downstream actions can update or comment on it without an extra lookup.

### Update record

Provide the record `id` and the fields you want to overwrite. The `id` is usually pulled from a previous **Create record** action or an upstream HTTP call.

### Post message

Post to the chatter of an existing record by `id`. The body supports HTML if you want to format the message.

## Limits

The Odoo action inherits TofuPilot's HTTP caps and respects Odoo's own per-user throttles.

* Odoo's own per-user rate limits apply.
* Per-action timeout, retries, and body-size limits match the [HTTP action](/docs/integrations/http).
