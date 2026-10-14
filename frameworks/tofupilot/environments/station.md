<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/environments/station -->
# Station
URL: /docs/frameworks/tofupilot/environments/station

Run the TofuPilot CLI as a station service on the factory floor, with deployed procedures, a live operator UI, and automatic report upload.

***

title: Station
description: Run the TofuPilot CLI as a station service on the factory floor, with deployed procedures, a live operator UI, and automatic report upload.
icon: Computer
--------------

import { Step, Steps } from 'fumadocs-ui/components/steps';

A station is the same TofuPilot CLI installed as a system service on a factory-floor machine. It runs deployed procedures with a live operator UI, auto-updates on every push to your production branch, and uploads reports to the dashboard.

## Install the station service

Create the station in your [dashboard](/docs/station) and open its **Install command**. The dashboard issues a single-use [setup token](/docs/access/setup-tokens) — valid for one hour, scoped to that one station — and hands you a ready-to-paste command.

Run that command on the bench machine. It installs the CLI, redeems the token, pulls the station's deployments, and registers the service so the station comes back on boot:

```bash title="Terminal"
curl -fsSL https://www.tofupilot.app/install | sh -s -- --token <setup-token-from-dashboard>
```

Behind the scenes it runs [`tofupilot login --token`](/docs/cli/login), which exchanges the setup token for long-lived station credentials and registers the CLI as a `systemd` unit on Linux or a `launchd` service on macOS. There is no second command to run.

To turn the bench back into a development machine, log in as yourself again — a plain [`tofupilot login`](/docs/cli/login) removes the station service:

```bash title="Terminal"
tofupilot login
```

## Run deployed procedures

Operators open and run the procedures [deployed](/docs/deployments) to the station, identify the unit under test, and provide inputs through the same operator UI used in [development](/docs/frameworks/tofupilot/environments/development).

## Deploy updates

Link your Git repository, then push to [deploy](/docs/deployments) updated procedure code to stations without interrupting production.

<Steps>
  <Step>
    **Develop** — Build and test procedures locally with the CLI in [development mode](/docs/frameworks/tofupilot/environments/development).
  </Step>

  <Step>
    **Push** — Commit and push to your production branch.
  </Step>

  <Step>
    **Deploy** — Select the commit and target stations from the dashboard.
  </Step>

  <Step>
    **Run** — Operators execute the updated procedures without downtime.
  </Step>
</Steps>

## Reports

Stations upload reports to your workspace automatically, queuing offline and syncing when connectivity returns so dashboards and analytics stay current.
