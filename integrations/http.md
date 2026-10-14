<!-- Source: https://www.tofupilot.com/docs/integrations/http -->
# HTTP
URL: /docs/integrations/http

Learn how to call any external API from a TofuPilot workflow with configurable headers, body, timeout, and retries.

***

title: HTTP
description: "Learn how to call any external API from a TofuPilot workflow with configurable headers, body, timeout, and retries."
----------------------------------------------------------------------------------------------------------------------------------

import { Callout } from 'fumadocs-ui/components/callout';

The HTTP integration lets you reach any URL from a workflow, so you can wire TofuPilot events into systems that do not have a first-party integration.

## Fields

The action exposes the standard HTTP request shape, with sensible defaults that keep most calls one or two fields away from working.

| Field                 | Required                    | Purpose                                             |
| --------------------- | --------------------------- | --------------------------------------------------- |
| **Method**            | Yes                         | `GET`, `POST`, `PUT`, `PATCH`, `DELETE`.            |
| **URL**               | Yes                         | Target URL. Supports `{{variable}}` interpolation.  |
| **Headers**           | No                          | Key/value pairs. Keys and values support variables. |
| **Body**              | No (Yes for POST/PUT/PATCH) | JSON or plain text. Supports variables.             |
| **Timeout (seconds)** | No                          | 1 to 60. Default 10.                                |
| **Retries**           | No                          | 0 to 3. Default 0.                                  |

The `Content-Type: application/json` header is set by default, and you can override it through the headers field.

## Outputs

After the request returns, downstream nodes can pull values out of the response through the action's output variables.

* `{{nodeId.status}}`: HTTP status code.
* `{{nodeId.body}}`: response body, parsed as JSON when the response was JSON and otherwise raw text.
* `{{nodeId.headers}}`: response headers as a JSON object.

Downstream actions can branch on `status` or pull values from `body` to drive subsequent steps.

## Securing your endpoint

TofuPilot does not sign outbound HTTP requests today, so you need to add your own check on the receiving side before processing anything sensitive.

<Callout type="warn">
  TofuPilot does not sign outbound HTTP requests. No HMAC header, no timestamp, no nonce. Your endpoint receives an unauthenticated request unless you add a secret.
</Callout>

You have three options for authenticating outbound calls:

* **Path token**: embed a token in the URL path such as `https://example.com/hooks/tofupilot/<token>` and reject mismatches.
* **Bearer header**: add `Authorization: Bearer <token>` to the headers and validate it server-side.
* **Body secret**: embed a shared secret in the JSON body and check it before processing.

For internal endpoints, you can restrict by source IP to TofuPilot's egress range. Contact support for the current allowlist.

## Limits

The HTTP action enforces a few caps so a single misbehaving endpoint cannot stall a workflow.

* Timeout: 1-60 seconds. Default 10.
* Body size: 1 MB on the request, 1 MB on the response.
* Retries: up to 3 with exponential backoff.
* Total action runtime: counted against the 5-minute workflow execution cap.
