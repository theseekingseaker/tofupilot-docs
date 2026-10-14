<!-- Source: https://www.tofupilot.com/docs/integrations/workflows -->
# Workflows
URL: /docs/integrations/workflows

Learn how to automate actions in TofuPilot when runs, units, batches, parts, revisions, or schedules trigger events with a visual editor.

***

title: Workflows
description: "Learn how to automate actions in TofuPilot when runs, units, batches, parts, revisions, or schedules trigger events with a visual editor."
--------------------------------------------------------------------------------------------------------------------------------------------------------

import { Step, Steps } from "fumadocs-ui/components/steps";
import { Callout } from "fumadocs-ui/components/callout";
import { Cards, Card } from "fumadocs-ui/components/card";
import { Tabs, Tab } from "fumadocs-ui/components/tabs";
import { Mail, Globe, Timer, PartyPopper, Calculator } from "lucide-react";
import {
  OdooIcon,
  LinearIcon,
  DiscordIcon,
  SlackIcon,
  InvenTreeIcon,
} from "@repo/ui/components/provider-icons";

A workflow ties a trigger event to a chain of actions, with optional control flow in between, and you author it from a visual editor. Workflows move through a status lifecycle of **Draft** → **Active** → **Paused** → **Archived**, and only `Active` workflows respond to events.

## Anatomy

Every workflow you build is composed of three parts that flow into one another in order.

<Steps>
  <Step>**Trigger**: an event fires.</Step>
  <Step>**Control flow** (optional): a filter, if/else, or switch shapes the path.</Step>
  <Step>**Actions**: nodes execute in sequence.</Step>
</Steps>

## Triggers

A workflow has exactly one trigger, and each trigger exposes event variables that downstream nodes can reference.

| Trigger            | Fires when                                                         |
| ------------------ | ------------------------------------------------------------------ |
| `run_created`      | A test run is created                                              |
| `unit_created`     | A unit is created                                                  |
| `batch_created`    | A batch is created                                                 |
| `part_created`     | A part is created                                                  |
| `revision_created` | A part revision is created                                         |
| `schedule`         | Time-based (daily/weekly/monthly), with timezone and day selection |
| `manual`           | On demand from the editor                                          |

<Callout>
  Rate limits: 100 automatic triggers per minute. 10 manual triggers per minute per organization.
</Callout>

## Control flow

Control-flow nodes sit between the trigger and the actions, and they decide which downstream paths execute.

| Node        | Behavior                                                   |
| ----------- | ---------------------------------------------------------- |
| **Filter**  | Single condition gate. Stops downstream when false.        |
| **If/Else** | Two-path branch on a true/false condition.                 |
| **Switch**  | Multi-path branch with up to 20 cases plus a default path. |

Conditions support `equals`, `contains`, `starts with`, `greater than`, `regex match`, `is null`, and more, and you can combine them with AND/OR.

## Actions

Actions are the nodes that do work, and they are grouped by the system they talk to.

### Messaging

Messaging actions push a message into a channel or inbox you already use.

<Cards>
  <Card icon={<Mail />} title="Send email" description="Up to 50 recipients with variable interpolation." />

  <Card icon={<SlackIcon />} title="Send Slack message" description="Post via incoming webhook URL." />

  <Card icon={<DiscordIcon />} title="Send Discord message" description="Post via webhook URL." />
</Cards>

### HTTP

The HTTP action calls any external API, so you can wire TofuPilot events into systems without a first-party integration.

<Cards>
  <Card icon={<Globe />} title="HTTP request" description="Call external APIs with configurable headers, body, timeout, retries." />
</Cards>

### Odoo

Odoo actions read and write records in your Odoo instance.

<Cards>
  <Card icon={<OdooIcon />} title="Create record" description="Create a record on any Odoo model." />

  <Card icon={<OdooIcon />} title="Update record" description="Update an existing record by id." />

  <Card icon={<OdooIcon />} title="Post message" description="Post on the chatter of an Odoo record." />
</Cards>

### InvenTree

InvenTree actions sync test data and stock movements with your InvenTree instance.

<Cards>
  <Card icon={<InvenTreeIcon />} title="Create stock item" description="Create a stock item for a part, reusing an existing serial." />

  <Card icon={<InvenTreeIcon />} title="Upload test result" description="Push a phase or run outcome onto a stock item's test report." />

  <Card icon={<InvenTreeIcon />} title="Update stock status" description="Change the status of a stock item." />

  <Card icon={<InvenTreeIcon />} title="Transfer stock" description="Move a stock item between locations." />

  <Card icon={<InvenTreeIcon />} title="Complete build output" description="Mark a build output as complete." />

  <Card icon={<InvenTreeIcon />} title="Print label" description="Trigger a label print." />
</Cards>

### Linear

Linear actions open and comment on tickets in your Linear organization.

<Cards>
  <Card icon={<LinearIcon />} title="Create issue" description="Optional assignee, labels, estimate." />

  <Card icon={<LinearIcon />} title="Add comment" description="Comment on an existing issue." />
</Cards>

### Utilities

Utility actions cover pacing, math, and a little bit of operator-facing polish.

<Cards>
  <Card icon={<Timer />} title="Delay" description="Pause execution for 1 second to 1 hour." />

  <Card icon={<Calculator />} title="Math operation" description="Add, subtract, multiply, divide, modulo." />

  <Card icon={<PartyPopper />} title="Celebrate" description="Trigger confetti for manual triggers." />
</Cards>

<Callout>
  HTTP request: 1-60 second timeout (default 10), 1 MB max body, up to 3 retries. Email: 50 recipients max.
</Callout>

## Variables

Variables let downstream nodes read values produced upstream, and you reference them with `{{nodeId.fieldKey}}`.

* **Trigger variables**: fields from the event.
* **Control flow variables**: filter result and the branch taken in if/else or switch.
* **Action output variables**: HTTP response status, created record id, and so on.

<Callout type="info">
  Missing variables render as `[not set]`.
</Callout>

## Editor

The editor is a node-and-edge canvas with shortcuts that mirror what you would expect from a graph editor.

* Click **+** below any node or use the bottom toolbar to add nodes.
* Click a node to configure it in the right panel.
* Connect nodes by dragging between handles.
* Add **notes** to annotate.
* Use **Organize actions** to auto-layout, and `Cmd+Z` to undo.

<Callout>
  Limits: 100 nodes, 200 edges, 50 notes per workflow.
</Callout>

## Execution history

The **Executions** tab lists past runs with their status, runtime, trigger source, and per-node results, so you can audit what each fire actually did.

<Callout>Maximum execution time: 5 minutes per workflow run.</Callout>

## Versioning

Edits auto-save and version automatically. While the workflow is in **Draft**, you can click **Save as new version** to create a named snapshot, and you can browse, preview, and restore prior versions from the version badge in the header.

## Webhook signing

TofuPilot handles webhooks in two directions: inbound from connected providers such as GitHub, GitLab, Bitbucket, and Linear, and outbound through the `http_request` action.

### Outbound webhooks

The `http_request` action sends to a URL you configure, with `{{variable}}` placeholders interpolated in the URL, headers, and body. The `Content-Type: application/json` header is set by default.

<Callout type="warn">
  Outbound HTTP requests are not signed. No HMAC, no timestamp, no nonce. Add your own check.
</Callout>

You have three options for authenticating the call on the receiving side:

* Long random token in the URL path such as `https://example.com/hooks/tofupilot/<token>`, with mismatches rejected.
* Custom `Authorization: Bearer <token>` header on the action, validated server-side.
* Shared secret in the JSON body, checked before processing.

### Inbound webhooks

TofuPilot verifies provider signatures using the secret stored on the installation, so you do not need a verifier on your side. You configure the secret on the provider's dashboard when installing the integration.

| Provider  | Endpoint                  | Header                | Algorithm                                                                    |
| --------- | ------------------------- | --------------------- | ---------------------------------------------------------------------------- |
| GitHub    | `/api/webhooks/github`    | `x-hub-signature-256` | HMAC-SHA256 of raw body, hex, prefixed `sha256=`                             |
| GitLab    | `/api/webhooks/gitlab`    | `x-gitlab-token`      | Shared secret matched against `sha256(token)`                                |
| Bitbucket | `/api/webhooks/bitbucket` | `x-hub-signature`     | HMAC-SHA256 of raw body, hex, prefixed `sha256=`                             |
| Linear    | `/api/webhooks/linear`    | `linear-signature`    | HMAC-SHA256 of `<timestamp>.<body>`, formatted `t=<timestamp>,s=<signature>` |

Each handler reads provider delivery headers for logging: `x-github-delivery`, `x-github-event`, `x-gitlab-event`, `x-gitlab-event-uuid`, `x-event-key`, and `x-request-id`.

### Verifying a webhook signature

Below is a reference implementation for the Linear scheme TofuPilot uses inbound, which you can adapt to your stack.

<Tabs items={['Python', 'Node']}>
  <Tab value="Python">
    ```python title="verify_webhook.py"
    import hmac
    import hashlib
    import time

    def verify(body: bytes, signature_header: str, secret: str) -> bool:
        parts = dict(p.split("=", 1) for p in signature_header.split(","))
        timestamp = parts.get("t")
        signature = parts.get("s")
        if not timestamp or not signature:
            return False

        now_ms = int(time.time() * 1000)
        if abs(now_ms - int(timestamp)) > 5 * 60 * 1000:
            return False

        payload = f"{timestamp}.".encode() + body
        expected = hmac.new(secret.encode(), payload, hashlib.sha256).hexdigest()
        return hmac.compare_digest(expected, signature)
    ```
  </Tab>

  <Tab value="Node">
    ```javascript title="verify-webhook.js"
    import { createHmac, timingSafeEqual } from 'node:crypto';

    export function verify(body, signatureHeader, secret) {
      const parts = Object.fromEntries(
        signatureHeader.split(',').map((p) => p.split('=', 2)),
      );
      const { t: timestamp, s: signature } = parts;
      if (!timestamp || !signature) return false;

      if (Math.abs(Date.now() - Number(timestamp)) > 5 * 60 * 1000) {
        return false;
      }

      const expected = createHmac('sha256', secret)
        .update(`${timestamp}.${body}`)
        .digest('hex');

      if (expected.length !== signature.length) return false;
      return timingSafeEqual(Buffer.from(expected), Buffer.from(signature));
    }
    ```
  </Tab>
</Tabs>

Always verify against the raw request body, because re-serializing the parsed JSON does not reproduce the signed bytes.

### Replay protection

The Linear handler rejects any request whose timestamp is more than **5 minutes** from server time. GitHub, GitLab, and Bitbucket inbound webhooks rely on the signature, and replay protection there comes from the provider's delivery id (`x-github-delivery`, `x-gitlab-event-uuid`, `x-request-id`).

For your own endpoints, apply the same 5-minute window once outbound signing is available. Until then, you can deduplicate by an idempotency key.
