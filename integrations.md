<!-- Source: https://www.tofupilot.com/docs/integrations -->
# Integrations
URL: /docs/integrations

Learn how to push runs, units, and yield from TofuPilot into Slack, Discord, email, Odoo, InvenTree, Linear, and custom HTTP endpoints.

***

title: Integrations
description: "Learn how to push runs, units, and yield from TofuPilot into Slack, Discord, email, Odoo, InvenTree, Linear, and custom HTTP endpoints."
------------------------------------------------------------------------------------------------------------------------------------------------------

import { Cards, Card } from 'fumadocs-ui/components/card';
import { Workflow, Mail, Globe } from 'lucide-react';
import { OdooIcon, LinearIcon, DiscordIcon, SlackIcon, InvenTreeIcon } from '@repo/ui/components/provider-icons';

TofuPilot integrates with the tools your team already uses through [Workflows](/docs/integrations/workflows). A workflow ties a trigger, such as a run landing, a unit being created, or a schedule firing, to one or more actions on a connected provider.

<Cards>
  <Card icon={<Workflow />} title="Workflows" href="/docs/integrations/workflows" description="The visual editor, triggers, control flow, variables, versioning, and execution history." />

  <Card icon={<Mail />} title="Email" href="/docs/integrations/email" description="Send transactional email to up to 50 recipients with variable interpolation." />

  <Card icon={<SlackIcon />} title="Slack" href="/docs/integrations/slack" description="Post messages to a Slack channel through an incoming webhook." />

  <Card icon={<DiscordIcon />} title="Discord" href="/docs/integrations/discord" description="Post messages to a Discord channel through a webhook URL." />

  <Card icon={<Globe />} title="HTTP" href="/docs/integrations/http" description="Call any external API on run, unit, or schedule events." />

  <Card icon={<OdooIcon />} title="Odoo" href="/docs/integrations/odoo" description="Create or update records and post to chatter from a workflow." />

  <Card icon={<InvenTreeIcon />} title="InvenTree" href="/docs/integrations/inventree" description="Create stock items, upload test results, update stock status, transfer stock, and trigger label prints." />

  <Card icon={<LinearIcon />} title="Linear" href="/docs/integrations/linear" description="Open issues and comment on existing tickets when a phase or run fails." />
</Cards>

## How integrations fit together

Every workflow you build has three parts that compose into a single pipeline:

1. **Trigger**: a TofuPilot event such as a run landing, a unit being created, or a schedule firing.
2. **Control flow** (optional): a filter, if/else, or switch that shapes the path downstream nodes take.
3. **Actions**: calls to integration providers that do the actual work.

Each action exposes its result as a variable, and downstream actions can read from it. For example, you can listen on a `run_created` trigger, filter to failing runs, open a Linear issue, and then post a Slack message that links to the new ticket.

| Integration | Direction          | Auth                     |
| ----------- | ------------------ | ------------------------ |
| Email       | Outbound           | TofuPilot-managed        |
| Slack       | Outbound           | Incoming webhook URL     |
| Discord     | Outbound           | Webhook URL              |
| HTTP        | Outbound           | Configurable per request |
| Odoo        | Outbound           | OAuth or API key         |
| InvenTree   | Outbound           | API token                |
| Linear      | Outbound + inbound | OAuth app                |

## Permissions

Connecting a provider and rotating its credentials requires the Admin or Owner role. Developers create, edit and delete workflows on top of the existing connections. Viewers can read workflows and connections, and Operators see neither. See [access roles](/docs/access/access-roles#permissions) for the full table.
