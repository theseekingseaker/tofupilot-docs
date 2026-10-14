<!-- Source: https://www.tofupilot.com/docs/integrations/discord -->
# Discord
URL: /docs/integrations/discord

Learn how to post messages to a Discord channel from a TofuPilot workflow using an incoming webhook URL when a trigger fires.

***

title: Discord
description: "Learn how to post messages to a Discord channel from a TofuPilot workflow using an incoming webhook URL when a trigger fires."
--------------------------------------------------------------------------------------------------------------------------------------------

import { Step, Steps } from 'fumadocs-ui/components/steps';

The Discord integration posts a message to a Discord channel whenever a workflow fires. It relies on Discord webhooks, so you do not need to host a bot or assign permissions to a service account.

## Connect Discord

Connecting Discord takes three steps that you complete in Discord first and then in TofuPilot.

<Steps>
  <Step>
    In Discord, open the target channel and navigate to **Settings > Integrations > Webhooks > New Webhook**.
  </Step>

  <Step>
    Name the webhook (for example, `TofuPilot`), pick an avatar, and copy the webhook URL it generates.
  </Step>

  <Step>
    In TofuPilot, open a workflow and add a **Send Discord message** action, then paste the webhook URL into the configuration panel.
  </Step>
</Steps>

## Fields

The action takes two required fields and two optional overrides that you can use to customize how the message appears in the channel.

| Field           | Required | Purpose                                                                      |
| --------------- | -------- | ---------------------------------------------------------------------------- |
| **Webhook URL** | Yes      | Discord webhook URL. The channel is encoded in the URL.                      |
| **Message**     | Yes      | Plain text. Supports `{{variable}}` interpolation. Discord renders markdown. |
| **Username**    | No       | Override the webhook's display name.                                         |
| **Avatar URL**  | No       | Override the webhook's avatar.                                               |

## Limits

Discord and TofuPilot both apply limits that you should be aware of when designing high-volume workflows.

* Discord enforces a global webhook rate limit of 30 messages per 60 seconds per channel.
* Message body: 2,000 characters max.
