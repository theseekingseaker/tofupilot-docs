<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/reports -->
# Reports
URL: /docs/frameworks/tofupilot/reports

Access TofuPilot test run results in the dashboard or stream them as JSON events from the CLI for programmatic consumption.

***

title: Reports
description: Access TofuPilot test run results in the dashboard or stream them as JSON events from the CLI for programmatic consumption.
icon: FileText
--------------

import { Callout } from 'fumadocs-ui/components/callout';

## Overview \[toc]

TofuPilot records every test run with phase outcomes, measurements, logs, and attachments, and uploads it to your organization.

You can access run results in two ways:

* **Dashboard** — every run appears in [Runs](/docs/fundamentals/test-data-model#run) with full phase, measurement, log, and attachment detail.
* **JSON event stream** — run your procedure with `--json` to consume results programmatically.

<Callout>
  If the upload fails (e.g. no network), the run is persisted to a local queue and retried automatically. See `tofupilot queue` for inspecting and exporting queued runs.
</Callout>

## JSON event stream

You can use `tofupilot run --json` to emit one JSON event per line on stdout while the run executes:

```bash
tofupilot run --json
```

```json
{"type":"run_started","procedure_id":"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx","protocol_version":"1"}
{"type":"phase_started","phase_key":"check_voltage","attempt":1,"started_at":"2026-06-10T14:30:00Z"}
{"type":"measurement_recorded","phase_key":"check_voltage","name":"voltage","value":3.3,"outcome":"unset","unit":"V"}
{"type":"phase_finished","phase_key":"check_voltage","outcome":"pass","attempt":1,"duration_ms":1776}
{"type":"run_finished","outcome":"pass","exit_code":0}
```

Each event is tagged with a `type` field. Key event types:

| Type                               | Description                                                         |
| ---------------------------------- | ------------------------------------------------------------------- |
| `run_started`                      | First event, carries the procedure id                               |
| `phase_started` / `phase_finished` | Phase lifecycle with outcome, timestamps, and duration              |
| `measurement_recorded`             | A measurement value was set (validation happens at phase close)     |
| `attachment_added`                 | A phase attached a file or data blob                                |
| `ui_request`                       | An operator UI prompt is waiting for input                          |
| `run_finished`                     | Terminal event with run outcome and exit code                       |
| `run_crashed`                      | The procedure subprocess died; includes a stderr tail for diagnosis |

This stream is line-delimited JSON, so you can pipe it into tools like `jq`:

```bash
tofupilot run --json | jq 'select(.type == "phase_finished")'
```
