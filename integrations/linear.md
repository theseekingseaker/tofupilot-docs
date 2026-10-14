<!-- Source: https://www.tofupilot.com/docs/integrations/linear -->
# Linear
URL: /docs/integrations/linear

Learn how to open Linear issues and comment on existing tickets from a TofuPilot workflow when a phase or run fails.

***

title: Linear
description: "Learn how to open Linear issues and comment on existing tickets from a TofuPilot workflow when a phase or run fails."
-----------------------------------------------------------------------------------------------------------------------------------

import { Step, Steps } from 'fumadocs-ui/components/steps';

The Linear integration creates and comments on Linear issues from a workflow, so failing runs and phases can open or update tickets without anyone leaving the dashboard.

## Connect Linear

Linear is connected through OAuth, so there is no API key to store on your side.

<Steps>
  <Step>
    In TofuPilot, open **Settings > Integrations > Linear** and click **Connect**.
  </Step>

  <Step>
    Authorize the TofuPilot OAuth app against your Linear organization from the consent screen Linear shows.
  </Step>

  <Step>
    Pick the team the workflow files issues into. You can connect multiple teams by repeating the flow.
  </Step>
</Steps>

Because the connection uses OAuth, there is no API key to rotate. You can revoke access at any time from Linear's **Settings > API > Authorized applications**.

## Actions

The integration exposes two actions that cover the two most common ticketing patterns from a test workflow.

| Action           | Effect                                                                  |
| ---------------- | ----------------------------------------------------------------------- |
| **Create issue** | Create an issue with optional assignee, labels, priority, and estimate. |
| **Add comment**  | Comment on an existing Linear issue by ID.                              |

### Create issue

The create-issue action supports the same metadata you would set from Linear itself, with `{{variable}}` interpolation across every text field.

* **Team**: required. Pick from the connected Linear teams.
* **Title**: required. Supports `{{variable}}` interpolation.
* **Description**: optional. Markdown supported.
* **Assignee**: optional.
* **Labels**: optional. Multi-select from the team's labels.
* **Priority**: optional. `Urgent`, `High`, `Medium`, `Low`, or `No priority`.
* **Estimate**: optional. Integer story points.

The created issue's `id` is exposed as `{{nodeId.id}}`, and the URL as `{{nodeId.url}}`, so downstream nodes can link back to the new ticket.

### Add comment

The add-comment action posts a comment onto an existing Linear issue and accepts the same markdown Linear's editor supports.

* **Issue ID**: required. Pulled from a prior **Create issue** action or supplied as a literal.
* **Body**: required. Markdown supported.

## Limits

The Linear action shares TofuPilot's HTTP caps and respects Linear's own per-organization throttles.

* Linear API rate limits apply per organization.
* Runtime, retries, and body-size match the [HTTP action](/docs/integrations/http).
