<!-- Source: https://www.tofupilot.com/docs/integrations/slack -->
# Slack
URL: /docs/integrations/slack

Learn how to post messages to a Slack channel from a TofuPilot workflow using an incoming webhook URL, with no OAuth app or token rotation.

***

title: Slack
description: "Learn how to post messages to a Slack channel from a TofuPilot workflow using an incoming webhook URL, with no OAuth app or token rotation."
preview: true
-------------

import { Step, Steps } from 'fumadocs-ui/components/steps';

The Slack integration posts a message to a Slack channel whenever a workflow fires. It uses Slack's incoming webhooks, so you do not need an OAuth app, scopes, or rotating tokens to wire it up.

## Connect Slack

You connect Slack once per channel by creating an incoming webhook and pasting the URL into a workflow action.

<Steps>
  <Step>
    In Slack, create an [incoming webhook](https://api.slack.com/messaging/webhooks) and pick the channel it will post into.
  </Step>

  <Step>
    Copy the webhook URL Slack generates.
  </Step>

  <Step>
    In TofuPilot, add a **Send Slack message** action to a workflow and paste the webhook URL into the configuration panel.
  </Step>
</Steps>

Repeat the flow once per channel. A single workflow can post to multiple channels by chaining Slack actions together.

## Fields

The action takes two required fields and two optional overrides that you can use to customize how the message appears in the channel.

| Field           | Required | Purpose                                                                           |
| --------------- | -------- | --------------------------------------------------------------------------------- |
| **Webhook URL** | Yes      | Slack incoming webhook URL. The channel is baked into the URL.                    |
| **Message**     | Yes      | Plain text. Supports `{{variable}}` interpolation. Markdown is rendered by Slack. |
| **Username**    | No       | Override the bot name.                                                            |
| **Icon emoji**  | No       | Override the bot icon (e.g. `:warning:`).                                         |

## Limits

Slack rate-limits incoming webhooks, so you should keep an eye on burst patterns when wiring high-volume triggers.

* Slack rate limits webhooks. Bursts past 1 message/second risk being dropped.
* Message body: 40 KB.
