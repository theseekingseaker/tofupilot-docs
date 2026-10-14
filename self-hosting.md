<!-- Source: https://www.tofupilot.com/docs/self-hosting -->
# Self-Hosting
URL: /docs/self-hosting

Learn how to run TofuPilot on your own servers with Docker so you keep full control over data, network, and security.

***

title: Self-Hosting
description: "Learn how to run TofuPilot on your own servers with Docker so you keep full control over data, network, and security."
------------------------------------------------------------------------------------------------------------------------------------

import { Callout } from 'fumadocs-ui/components/callout';
import { Cards, Card } from 'fumadocs-ui/components/card';
import { Settings, Box, Server, CircleArrowUp } from 'lucide-react';

When you self-host TofuPilot, you run the dashboard, deployer, and Postgres in your own infrastructure. Docker Compose deploys them on a single host or in Kubernetes, with one organization per instance.

<Cards>
  <Card icon={<Settings />} title="Configure" href="/docs/self-hosting/configure" description="Set domains, SSL, authentication providers, and the Git provider for your self-hosted instance via the .env file." />

  <Card icon={<Box />} title="Deploy" href="/docs/self-hosting/deploy" description="Run the deploy script, create the first admin account, and start uploading Runs to your self-hosted instance." />

  <Card icon={<Server />} title="Operations" href="/docs/self-hosting/operations" description="Manage your self-hosted instance: license, upgrades, reconfiguration, backups, and uninstall." />

  <Card icon={<CircleArrowUp />} title="Upgrade" href="/docs/self-hosting/upgrade" description="Upgrade to the stable release, pin a specific version, and enable realtime views on older instances." />
</Cards>

## When to self-host

The managed cloud at [tofupilot.app](https://www.tofupilot.app) is updated continuously, and most teams should start there. You self-host when you need data on-premises or have to comply with specific IT policies.

## Register your instance

Self-hosted instances run free for as long as you need them. For upgrade options, see [Pricing](https://www.tofupilot.com/pricing).

Follow these steps to register a new instance:

1. Sign in to [TofuPilot Orbit](https://www.tofupilot.com/orbit).
2. Under **Self-Hosting**, click **+** to create a new instance.
3. Copy the deploy command, which contains your license key.

The license key authenticates your instance and unlocks access to the Docker image.

## System requirements

TofuPilot runs on any standard Linux server. The minimums below cover small fleets, and you can scale up as your run volume grows.

| Resource   | Minimum                                                    |
| ---------- | ---------------------------------------------------------- |
| OS         | Ubuntu 20.04+ or Debian (64-bit, x86\_64)                  |
| Docker     | Standard (root) Docker Engine with the Compose plugin      |
| CPU        | 2+ cores                                                   |
| RAM        | 4 GB                                                       |
| Storage    | 40 GB+ (scale with runs, attachments, and build artifacts) |
| Privileges | Sudo access                                                |
| Network    | Ports 80 and 443 open                                      |

Every other service runs inside Docker and is not exposed.

<Callout type="warn">
  Use **standard (root) Docker**, not rootless. The deployments build worker runs a privileged container that rootless Docker cannot start. If you do not need the build pipeline, rootless Docker runs the rest of the stack (including realtime) fine.
</Callout>

You also need three subdomains pointing at the server — one each for the app, file storage, and realtime WebSocket. See [Domains](/docs/self-hosting/configure#domains).
