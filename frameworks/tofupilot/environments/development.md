<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/environments/development -->
# Development
URL: /docs/frameworks/tofupilot/environments/development

Install the TofuPilot CLI and run procedures locally in development mode with a live operator UI while you build and debug your test.

***

title: Development
description: Install the TofuPilot CLI and run procedures locally in development mode with a live operator UI while you build and debug your test.
icon: PencilRuler
-----------------

import { Tab, Tabs } from 'fumadocs-ui/components/tabs';

In development mode you run procedures from your own machine with the TofuPilot CLI. Runs execute locally with the same operator UI a [station](/docs/frameworks/tofupilot/environments/station) shows, so you can build and debug a test before deploying it to the factory floor.

## Install the CLI

The CLI is a single binary that runs on macOS, Linux, and Windows. Install it with a one-line script:

<Tabs items={['macOS / Linux', 'Windows']}>
  <Tab value="macOS / Linux">
    ```bash
    curl -fsSL https://www.tofupilot.app/install | sh
    ```
  </Tab>

  <Tab value="Windows">
    ```powershell
    $p = "$env:TEMP\tp-install.ps1"; irm https://www.tofupilot.app/install.ps1 -OutFile $p; powershell -ExecutionPolicy Bypass -File $p; ri $p -EA 0
    ```
  </Tab>
</Tabs>

Authenticate once with [`tofupilot login`](/docs/cli/login). See the [CLI reference](/docs/cli) for every subcommand.

## Run in development mode

Point `tofupilot run` at a procedure directory to execute it locally. The run starts immediately, without contacting the dashboard:

```bash title="Terminal"
tofupilot run ./my-test
```

The CLI bootstraps Python through `uv`, schedules the phases, and streams the operator UI to your terminal. See [`tofupilot run`](/docs/cli/run) for the full set of flags.

## Upload runs

To sync a development run to a procedure on your dashboard, [link the directory](/docs/cli/link) once and run with `--upload`:

```bash title="Terminal"
tofupilot link
tofupilot run ./my-test --upload
```

`tofupilot link` writes a `tofupilot.json` file binding the local directory to the remote procedure, and `--upload` sends the run there.

## Switch between development and station

The CLI's mode is determined by who it is logged in as — there is a single set of credentials, and logging in again replaces the previous identity.

To turn a development machine into a [station](/docs/frameworks/tofupilot/environments/station), run the install command from the station's [setup token](/docs/access/setup-tokens) page. It redeems the token, pulls the station's deployments, starts the daemon, and registers the service so the station comes back on boot:

```bash title="Development → Station"
curl -fsSL https://www.tofupilot.app/install | sh -s -- --token <setup-token-from-dashboard>
```

To go back to development, log in as yourself again. A plain `tofupilot login` removes the station service and the CLI is back in development mode, where `tofupilot run ./my-test` executes procedures locally:

```bash title="Station → Development"
tofupilot login
```
