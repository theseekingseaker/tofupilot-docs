<!-- Source: https://www.tofupilot.com/docs/access/setup-tokens -->
# Setup Tokens
URL: /docs/access/setup-tokens

Learn how TofuPilot setup tokens pre-authorize the CLI as a station with one-hour, single-use credentials redeemed on the test machine.

***

title: Setup Tokens
description: "Learn how TofuPilot setup tokens pre-authorize the CLI as a station with one-hour, single-use credentials redeemed on the test machine."
------------------------------------------------------------------------------------------------------------------------------------------------------

import { Steps, Step } from 'fumadocs-ui/components/steps';
import { Callout } from 'fumadocs-ui/components/callout';
import { Cards, Card } from 'fumadocs-ui/components/card';
import { Key, Computer } from 'lucide-react';

A setup token is a one-time credential that lets the CLI register as a [station](/docs/station) without an interactive browser login. The dashboard issues the token, the install script redeems it, and the CLI saves the station-scoped [API key](/docs/access/api-keys) and starts the daemon. Use setup tokens for headless installs and provisioning scripts.

Issuing a setup token requires the Developer, Admin, or Owner role.

## Lifecycle

Setup tokens are deliberately short-lived and narrowly scoped, so a leaked token has limited blast radius.

| Property    | Value                                            |
| ----------- | ------------------------------------------------ |
| TTL         | 1 hour from issue                                |
| Uses        | Single-use (consumed on redeem)                  |
| Scope       | One station, station-scoped key only             |
| Issued by   | Dashboard: **Stations -> \<Station> -> Install** |
| Redeemed by | `tofupilot login --token <TOKEN>`                |

The token is a 64-character opaque string scoped to a single station. Once redeemed, it is marked consumed and rejected on any further use.

## Issue a token

<Steps>
  <Step>
    Open **Stations** and either pick an existing station or create a new one with **New Station**.
  </Step>

  <Step>
    Click **Install command** on the station detail page.
  </Step>
</Steps>

The dashboard generates a fresh token and copies the full install command to your clipboard:

```bash title="copied-from-dashboard"
curl -fsSL https://www.tofupilot.app/install | sh -s -- --token <SETUP_TOKEN>
```

The payload includes `--url` automatically for [self-hosted](/docs/self-hosting) instances, while the SaaS domain (`tofupilot.app`) leaves it out.

## Redeem on the test machine

Run the install command on the station hardware. The install script downloads the CLI, then invokes the redeem flow:

```bash title="under-the-hood"
tofupilot login --token <SETUP_TOKEN>
```

The CLI sends the token along with a local hardware fingerprint (hostname, OS, platform, MAC, CLI version). The server validates the token, consumes it, mints an API key scoped to the station, and returns credentials the CLI saves at `~/.tofupilot/credentials.json`.

## What the token grants

The redeemed key is a station API key, not a user key, so its permissions are narrowly scoped. It can:

* Upload [runs](/docs/fundamentals/test-data-model#run) for procedures linked to this station.
* Pull [deployments](/docs/deployments) targeted at this station.
* Read its own station record via `stations.get_current`.
* Create procedure versions for linked procedures.

It cannot manage users, mutate other stations, or read organization-wide data. For the full station permission matrix, see [access roles](/docs/access/access-roles).

<Callout>
  Setup tokens are single-use and time-boxed. If the install script fails halfway, generate a fresh token rather than retrying with the consumed one. The CLI prints a hint pointing back to the Setup page when it hits a consumed or expired token.
</Callout>

## Rotation

Delete the station's installation from the dashboard and re-run the install command on the test machine. That issues a new token, redeems it, and overwrites local credentials, so the old API key stops working immediately.

<Cards>
  <Card icon={<Key />} title="API keys" href="/docs/access/api-keys" description="Authenticate scripts, stations, and CI with scoped, revocable keys." />

  <Card icon={<Computer />} title="Station" href="/docs/station" description="Install the CLI on a factory bench to run tests unattended." />
</Cards>
