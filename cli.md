<!-- Source: https://www.tofupilot.com/docs/cli -->
# CLI
URL: /docs/cli

Learn how the TofuPilot CLI runs every subcommand for procedures, runs, units, stations, and the REST API from a single binary on macOS, Linux, and Windows.

***

title: CLI
description: "Learn how the TofuPilot CLI runs every subcommand for procedures, runs, units, stations, and the REST API from a single binary on macOS, Linux, and Windows."
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Learn how the TofuPilot CLI runs every subcommand for procedures, runs, units, stations, and the REST API from a single binary on macOS, Linux, and Windows.

import { Cards, Card } from 'fumadocs-ui/components/card';
import { Terminal, Computer, KeyRound } from 'lucide-react';

The `tofupilot` CLI is a single binary that runs procedures locally, manages every resource through the REST API, and powers a factory [Station](/docs/station) when installed as a system service. You get one binary, one credential file, and a consistent flag layout across every subcommand.

The CLI is free to download and install on any machine, and [`tofupilot run ./procedure.yaml`](/docs/cli/run) executes a procedure locally without an account. Running a pulled deployment instead needs one. Commands that read or write data on TofuPilot need [`tofupilot login`](/docs/cli/login).

## Install

The CLI runs on macOS, Linux, and Windows, and you install it with a one-line script.

```bash title="macOS / Linux"
curl -fsSL https://www.tofupilot.app/install | sh
```

```powershell title="Windows"
$p = "$env:TEMP\tp-install.ps1"; irm https://www.tofupilot.app/install.ps1 -OutFile $p; powershell -ExecutionPolicy Bypass -File $p; ri $p -EA 0
```

After install, run `tofupilot --help` to see every subcommand, or jump straight into [`tofupilot login`](/docs/cli/login) to authenticate.

<Cards>
  <Card icon={<KeyRound />} title="tofupilot login" href="/docs/cli/login" description="Authenticate the CLI and start using every subcommand." />

  <Card icon={<Terminal />} title="tofupilot run" href="/docs/cli/run" description="Execute a procedure locally with the same operator UI as a station." />

  <Card icon={<Computer />} title="Station" href="/docs/station" description="Install the CLI as a system service on a factory bench." />
</Cards>

## JSON output

Every command accepts the global `--json` flag, which switches output to machine-readable JSON on stdout. Errors become a `{"type":"error","message":"..."}` object on stderr, and `tofupilot run --json` emits an NDJSON event stream on stdout, one event per line.

```bash title="json output"
tofupilot runs ls --limit 5 --json
```

## Environment variables

| Variable                  | Effect                                                         |
| ------------------------- | -------------------------------------------------------------- |
| `TOFUPILOT_NO_UPDATE`     | Set to `1` or `true` to disable automatic update checks.       |
| `TOFUPILOT_LOCAL_UI_PORT` | Overrides the default loopback port of the operator UI server. |

## Framework detection

`tofupilot run` scans the procedure directory, picks the framework that fits, and runs the suite under a framework-specific connector that maps outcomes, [Measurements](/docs/fundamentals/test-data-model#measurement), [Logs](/docs/fundamentals/test-data-model#log), and [Attachments](/docs/fundamentals/test-data-model#attachment) to the dashboard.

The CLI walks the directory in a fixed order, and the first framework it recognizes wins.

```
procedure.yaml  →  TofuPilot Framework
openhtf in deps →  OpenHTF
pytest config   →  Pytest
.robot files    →  Robot Framework
main.py only    →  Plain Python
```

When a repo carries both an [OpenHTF](/docs/frameworks/openhtf) suite and [pytest](/docs/frameworks/pytest) tests, OpenHTF wins because pytest is assumed to be unit-test tooling for the OpenHTF code. When a repo carries pytest tests next to `.robot` files, pytest wins for the same reason.
