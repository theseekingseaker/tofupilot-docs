<!-- Source: https://www.tofupilot.com/docs/station -->
# Station
URL: /docs/station

Learn how to install and run the TofuPilot CLI as a station so a physical test bench can pull deployments and stream every run back to the dashboard.

***

title: Station
description: "Learn how to install and run the TofuPilot CLI as a station so a physical test bench can pull deployments and stream every run back to the dashboard."
--------------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Tabs, Tab } from 'fumadocs-ui/components/tabs';
import { Steps, Step } from 'fumadocs-ui/components/steps';

A station is the CLI running unattended on a physical test bench. Each station has its own credentials, pulls [Deployments](/docs/deployments) from a linked Git repo, executes [Procedures](/docs/fundamentals/test-data-model#procedure), and streams every [Run](/docs/fundamentals/test-data-model#run) back to the dashboard.

## Register a station

To register a station, you create it in the dashboard and then install on the test machine. The install command embeds a one-hour setup token, and the CLI exchanges it for long-lived credentials on first boot.

<Steps>
  <Step>
    Open **Stations > New Station**, name it, and copy the install command.
  </Step>

  <Step>
    Run the command on the test machine.

    <Tabs items={['macOS / Linux', 'Windows']}>
      <Tab value="macOS / Linux">
        ```bash title="install"
        curl -fsSL https://www.tofupilot.app/install | sh -s -- --token <SETUP_TOKEN>
        ```
      </Tab>

      <Tab value="Windows">
        ```powershell title="install"
        $p = "$env:TEMP\tp-install.ps1"; irm https://www.tofupilot.app/install.ps1 -OutFile $p; powershell -ExecutionPolicy Bypass -File $p --token <SETUP_TOKEN>; ri $p -EA 0
        ```
      </Tab>
    </Tabs>
  </Step>

  <Step>
    Link procedures to the station from the dashboard.
  </Step>
</Steps>

The setup token expires one hour after issue, so when it expires, generate a new one from the station's row in the dashboard.

## Metadata

Metadata follows PATCH semantics, so omitted keys are preserved and `null` deletes a key.

* Up to 50 keys per station.
* Keys: 1-40 chars, `^[a-zA-Z0-9_.:+-]+$`.
* Values: `string` (max 50000 chars), `number`, or `boolean`. The type is auto-detected.

The station detail page exposes the metadata editor, and every change is recorded in the station activity.

## API

Stations are exposed at `/api/v2/stations` with `create`, `list`, `get`, `update`, and `remove`. See the [REST API](/docs/api/rest-api) reference for the full surface.

```python title="create_station.py"
from tofupilot.v2 import TofuPilot

client = TofuPilot()

station = client.stations.create(name="Test Bench #1")
```

| Field          | Required | Description                                                |
| -------------- | -------- | ---------------------------------------------------------- |
| `name`         | Yes      | 1-60 characters.                                           |
| `procedure_id` | No       | Link the station to a procedure at creation.               |
| `metadata`     | No       | Key/value pairs, up to 50 keys. See [Metadata](#metadata). |

| Endpoint | Response                                                                                                                                                      |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `create` | `{ id }`                                                                                                                                                      |
| `get`    | `id`, `name`, `api_key`, `procedures` (each with `id`, `name`, `runs_count`, `deployment`), `organization_slug`, `team`, `metadata`                           |
| `list`   | `data[]` of `{ id, name, procedures (id, name), procedures_count, team, metadata (with include_metadata=true) }`, plus `meta.has_more` and `meta.next_cursor` |

## Linked procedures

A station can be linked to one or many procedures, and deployment rollout only reaches stations with the procedure linked.

When you unlink a procedure, new deployments stop, but runs in flight finish against the version they started with.

## Operator UI and uptime

You can open the [Operator UI](/docs/frameworks/tofupilot/operator-ui) for a station from the **Stations** tab, and the dashboard shows real-time connectivity, the active deployment, and 90-day uptime history.

## Stopping a run

A stop always runs the procedure's teardown phases before the process exits, so an execution-scoped `power_off` still reaches the bench. The supported stop paths are Ctrl-C or Ctrl-Break in the station's console, SIGTERM on Linux and macOS, `tofupilot service stop`, and Stop or Exit in the operator UI. For a signal (Ctrl-C, Ctrl-Break, SIGTERM) the first one interrupts the running phases and runs the teardown, a second one within three seconds is ignored, a later second one abandons the teardown still running, and a third exits the process. `tofupilot service stop` and the operator UI stop run the teardown too. For the TofuPilot framework and OpenHTF, a run stopped by a signal, by `tofupilot service stop` or by Stop in the operator UI exits with code 130 and is uploaded with outcome `ABORTED`, and the second signal still releases the plugs and uploads what completed. Exit in the operator UI gives the teardown three seconds, then abandons it the same way and exits shortly after, so the upload of that last run is not guaranteed. An OpenHTF run stopped or killed by the other paths is left without an upload only when its process does not exit within seven seconds of the forced abort, which a plug `tearDown` that blocks can cause. A TofuPilot framework run the procedure stops itself (`phase.stop()`, or a `then` rule ending in `stop`) is uploaded as `ABORTED` too, with exit code 3, so a script driving the CLI can tell the two apart.

Closing the console window on Windows is not a supported stop. Windows gives the process about five seconds after the close before it terminates it, and a logoff or shutdown is not delivered to an interactive console program at all. The CLI uses those seconds for a hurried teardown, but nothing guarantees the power-off lands. Fixture safety on the bench has to come from the instrument itself: arm the power supply's output watchdog (`OUTP:PROT:WDOG ON` with a delay in `OUTP:PROT:WDOG:DEL` on Keysight supplies) in the setup phase so the outputs trip when the station stops talking to it, whatever ended the process.

## Held instrument connections

Plugs declared with `scope: station` connect once and stay connected across every run on the station — back-to-back units skip the reconnection cost for slow links (VISA sessions, TCP, serial). The instance is health-checked before each run and respawned if the plug definition or deployment changed. See [plug scope](/docs/frameworks/tofupilot/plugs#scope).

## Offline behavior

Stations queue runs locally when offline, and uploads resume when the network returns. See [Offline upload](/docs/station/offline-upload) for the full lifecycle.
