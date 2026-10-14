<!-- Source: https://www.tofupilot.com/docs/self-hosting/upgrade -->
# Upgrade
URL: /docs/self-hosting/upgrade

Learn how to upgrade a self-hosted TofuPilot instance, back up your data first, and enable realtime views when moving from an older version.

***

title: Upgrade
description: "Learn how to upgrade a self-hosted TofuPilot instance, back up your data first, and enable realtime views when moving from an older version."
-----------------------------------------------------------------------------------------------------------------------------------------------------------

import { DynamicCodeBlock } from '@/components/themed-dynamic-code-block';
import { Callout } from 'fumadocs-ui/components/callout';

You upgrade by re-running the deploy script. It pulls the Docker images from the stable release channel, applies any database migrations, and restarts the stack. Data and auto-generated secrets in `.env` are preserved across the upgrade.

<Callout type="warn">
  Back up your data before upgrading. Downgrades are not supported, so a backup is the only way to roll back. See [Backups](/docs/self-hosting/operations#backups).
</Callout>

## Release channels

TofuPilot cloud releases continuously, and every release publishes a versioned Docker image. Self-hosted instances follow a separate stable channel instead of tracking each release:

* **stable** is the default. About once a month, a version that has run in production is promoted to the channel. Running the deploy script with no options installs or upgrades to the current stable release. Security fixes and critical bug fixes are promoted out of cycle when needed.
* **A pinned version** opts out of the channel. Passing `--version <version>` installs that exact release, including releases newer than the current stable one. Use `--list-versions` to see what is available; the stable release is marked in the listing.

If your instance runs a version newer than the current stable release, for example after pinning a recent version, re-running the script keeps your version and the instance rejoins the channel at the next promotion. Each stable promotion ships with release notes describing what changed for users and what changed operationally.

## Upgrade to the stable release

SSH into your server, change to the directory that holds your `.env`, and run:

<DynamicCodeBlock lang="bash" code="curl -fsSL https://www.tofupilot.com/deploy | bash" />

The script reports the new running version to Orbit once the upgrade finishes. You can also re-deploy or trigger an upgrade from the instance menu in [Orbit](https://www.tofupilot.com/orbit).

## Pin a specific version

To install a fixed version instead of the stable release, for example a newer release that has not been promoted yet, list the available versions:

<DynamicCodeBlock lang="bash" code="curl -fsSL https://www.tofupilot.com/deploy | bash -s -- --list-versions" />

Then pass the version explicitly. Downgrades are rejected; re-running the currently installed version is accepted, which you can use to repair an install:

<DynamicCodeBlock lang="bash" code="curl -fsSL https://www.tofupilot.com/deploy | bash -s -- --version <version>" />

## Enable realtime views

Realtime updates over WebSocket were added in **2.33**. When you upgrade an instance created before that version, add the realtime subdomain so live station status and telemetry, dashboard updates, and streaming build logs work:

1. Create a DNS A record for `CENTRIFUGO_DOMAIN_NAME` pointing to your server. It defaults to `realtime.<DOMAIN_NAME>` (e.g. `realtime.tofupilot.yourcompany.com`).
2. Re-run the deploy script. It provisions the `tofupilot-centrifugo` container and generates the `CENTRIFUGO_*` secrets in `.env` automatically.
3. Confirm the realtime server is up:

<DynamicCodeBlock lang="bash" code="docker compose logs tofupilot-centrifugo --tail 50" />

4. Verify the endpoint is reachable over TLS. A `400` response is expected — it means the certificate issued and the server is waiting for a WebSocket upgrade:

<DynamicCodeBlock lang="bash" code="curl -s -o /dev/null -w '%{http_code}' https://<CENTRIFUGO_DOMAIN_NAME>/connection/websocket" />

<Callout type="info">
  The rest of the application works without the realtime subdomain; only the live views fall back to manual refresh. See [Realtime views not updating](/docs/self-hosting/operations#realtime-views-not-updating) for troubleshooting.
</Callout>

## Verify the upgrade

Check that every container is running and the dashboard reports the new version:

<DynamicCodeBlock
  lang="bash"
  code="docker compose ps
docker compose logs tofupilot-dashboard --tail 100"
/>

If a container fails to start, inspect its logs with `docker compose logs <container>` and see [Troubleshooting](/docs/self-hosting/operations#troubleshooting).
