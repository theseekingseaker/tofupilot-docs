<!-- Source: https://www.tofupilot.com/docs/self-hosting/operations -->
# Operations
URL: /docs/self-hosting/operations

Learn how to manage your self-hosted TofuPilot instance: license, upgrades, reconfiguration, backups, and uninstall.

***

title: Operations
description: "Learn how to manage your self-hosted TofuPilot instance: license, upgrades, reconfiguration, backups, and uninstall."
-----------------------------------------------------------------------------------------------------------------------------------

import { DynamicCodeBlock } from '@/components/themed-dynamic-code-block';

This page covers the lifecycle of a [self-hosted](/docs/self-hosting) instance: license, upgrades, runtime management, and clean shutdown.

## License

Your instance pulls its license from Orbit on first boot. The license controls plan, usage limits, and feature availability.

### View your license

To inspect your current license, open **Settings > Subscription** in your dashboard. You see your plan, license status, and current usage.

### Refresh your license

When you upgrade your plan or renew, click the refresh button on the license card to sync the latest license from Orbit.

### License expiry

Licenses are valid for a set period. As a license approaches or passes its expiry date, the instance gradually restricts write operations.

| State                | What changes                                                                       |
| -------------------- | ---------------------------------------------------------------------------------- |
| **Active**           | Normal operation.                                                                  |
| **Expiring Soon**    | A warning banner appears, but nothing is restricted yet.                           |
| **Write Restricted** | Creating new users and stations is blocked. Existing data and runs are unaffected. |
| **Expired**          | A renewal prompt appears on every page. Contact sales to renew.                    |

### Air-gapped activation

When your server has no internet, you can paste the license manually:

1. In [TofuPilot Orbit](https://www.tofupilot.com/orbit), open the instance menu and select **Copy license token**.
2. In your dashboard, go to **Settings > Subscription**.
3. Click the upload button on the license card.
4. Paste the token and click **Activate**.

### Manage from Orbit

[Orbit](https://www.tofupilot.com/orbit) gives you an overview of every self-hosted instance, with running version, domain, and last check-in. From the instance menu you can open the dashboard, re-deploy, trigger an upgrade, copy the license token for air-gapped activation, or delete the instance.

## Upgrade \[#update]

To move to a newer version, back up your data and re-run the deploy script. Downgrades are not supported, which protects your data. See [Upgrade](/docs/self-hosting/upgrade) for the full procedure, version pinning, realtime setup on older instances, and post-upgrade checks.

## Reconfigure

When you need to change configuration after deploying, such as adding an OAuth provider or updating SMTP, edit `.env` and re-run the deploy command:

<DynamicCodeBlock lang="bash" code="curl -fsSL https://www.tofupilot.com/deploy | bash" />

Data and auto-generated secrets are preserved across re-runs.

## Manage with Docker Compose

You can manage the instance from the server with Docker Compose:

<DynamicCodeBlock
  lang="bash"
  code="docker compose ps          # Check service status
docker compose logs -f     # Follow live logs
docker compose restart     # Restart all services
docker compose down        # Stop all services
docker compose up -d       # Start all services"
/>

When something is not working as expected, check the application logs first:

<DynamicCodeBlock lang="bash" code="docker compose logs tofupilot-dashboard --tail 100" />

## Backups

Your data lives in two Docker volumes. Back them up on a regular schedule.

| Volume                   | Contents                                         |
| ------------------------ | ------------------------------------------------ |
| `tofupilot-pg-data`      | PostgreSQL database                              |
| `tofupilot-seaweed-data` | Uploaded files, attachments, and build artifacts |

A third volume, `tofupilot-deployer-docker`, holds the build worker's local Docker state (sandbox images it builds on demand). It is regenerated automatically and does not need backing up.

## Troubleshooting

### Realtime views not updating

Live station status, telemetry, and streaming build logs connect over WebSocket to `CENTRIFUGO_DOMAIN_NAME`. If those views fall back to manual refresh:

1. Confirm the realtime subdomain has a DNS A record pointing at the server and that its TLS certificate issued (`docker compose logs tofupilot-proxy` shows the Let's Encrypt exchange).
2. Check the realtime server is up: `docker compose logs tofupilot-centrifugo --tail 50`.
3. Verify the browser can reach `wss://<CENTRIFUGO_DOMAIN_NAME>/connection/websocket` (a `400` from a plain HTTPS request is expected — it means the endpoint is reachable and waiting for a WebSocket upgrade).

The rest of the application works without realtime; only the live views are affected.

### Deployments not building

The deployments pipeline runs in the `tofupilot-deployer` container:

1. It needs **standard (root) Docker**. Under rootless Docker it will not start — check `docker compose ps` and use a root Docker installation if it is missing.
2. Inspect build activity: `docker compose logs tofupilot-deployer --tail 100`.
3. On the first build the worker builds its sandbox image inside its own Docker daemon, which takes a few minutes; subsequent builds are fast.

## Uninstall

To remove TofuPilot from your server completely, run:

<DynamicCodeBlock lang="bash" code="curl -fsSL https://www.tofupilot.com/deploy | bash -s -- --uninstall" />

This deletes all containers, volumes, configuration, and data. To also revoke the license, delete the instance from [TofuPilot Orbit](https://www.tofupilot.com/orbit).
