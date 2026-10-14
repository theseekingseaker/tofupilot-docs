<!-- Source: https://www.tofupilot.com/docs/integrations/email -->
# Email
URL: /docs/integrations/email

Learn how to send transactional email from a TofuPilot workflow when a trigger fires, with no SMTP credentials or DNS records on your domain.

***

title: Email
description: "Learn how to send transactional email from a TofuPilot workflow when a trigger fires, with no SMTP credentials or DNS records on your domain."
------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Callout } from 'fumadocs-ui/components/callout';

The Email integration sends a transactional message from a workflow whenever a trigger fires. Delivery is managed by TofuPilot, so you do not need SMTP credentials or an SPF or DKIM record on your domain.

## Fields

The action takes three required fields and two optional copy fields that you can use to send the message to additional recipients.

| Field       | Required | Purpose                                                                               |
| ----------- | -------- | ------------------------------------------------------------------------------------- |
| **To**      | Yes      | Up to 50 recipient addresses, comma-separated. Supports `{{variable}}` interpolation. |
| **Cc**      | No       | Carbon copy. Counted toward the 50 cap.                                               |
| **Bcc**     | No       | Blind carbon copy. Counted toward the 50 cap.                                         |
| **Subject** | Yes      | Plain text. Supports variables.                                                       |
| **Body**    | Yes      | Plain text or HTML. Supports variables.                                               |

## Variables

You can reference values from the trigger or any earlier action with `{{nodeId.fieldKey}}` syntax. Common examples include:

* `{{trigger.run.name}}`
* `{{trigger.unit.serial_number}}`
* `{{trigger.run.outcome}}`: one of `PASS`, `FAIL`, `ERROR`, `TIMEOUT`, or `ABORTED`.
* `{{http_request.status}}`: HTTP status from an upstream HTTP action.

Missing variables render as `[not set]` at execution time.

<Callout type="info">
  Include a link to the run page in the body: `https://tofupilot.app/{org}/runs/{{trigger.run.id}}`.
</Callout>

## Limits

The email action has a few caps you should keep in mind when sending to large recipient lists or attaching long bodies.

* 50 recipients across To/Cc/Bcc.
* Body size: 1 MB.
* Delivery: best-effort within 30 seconds.
