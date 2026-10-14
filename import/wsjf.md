<!-- Source: https://www.tofupilot.com/docs/import/wsjf -->
# Import WATS WSJF Reports
URL: /docs/import/wsjf

Learn how to import Virinco WATS reports in WSJF (JSON) format into TofuPilot with automatic detection, migrating test history with no column mapping.

***

title: Import WATS WSJF Reports
sidebarTitle: WSJF
description: "Learn how to import Virinco WATS reports in WSJF (JSON) format into TofuPilot with automatic detection, migrating test history with no column mapping."
---------------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Callout } from 'fumadocs-ui/components/callout';

WATS is a **structured** format: TofuPilot reads a Virinco WATS report directly, so importing one needs no column mapping. The report's step tree becomes a TofuPilot [Run](/docs/fundamentals/test-data-model#run) with phases and measurements.

Use this to migrate test history off WATS without re-running anything.

<FormatSpec encoding="JSON" extension=".json" detection="Auto-detected (sn, pn, result, start fields)" origin="Virinco AS, WATS Standard JSON Format" multiRun="no" mapsTo="Run, phases, measurements, limits, unit, part, revision, metadata" api="POST /v2/import (importer: WATS)" />

## Supported format

This page covers **WSJF** (WATS Standard JSON Format). For the XML variant, see [WSXF](/docs/import/wsxf).

## History

WSJF and [WSXF](/docs/import/wsxf) are interchange formats defined by Virinco AS (Norway) for its WATS manufacturing-data system. WSXF (XML) is the older format; WSJF (JSON) is the newer equivalent introduced to simplify third-party integration. Both are current and actively used: the WATS Client auto-imports files dropped into its standard-format folders, and both can be posted through the WATS REST API.

## File structure vs the TofuPilot model

A WSJF report models either a test (UUT) or repair (UUR) record. TofuPilot imports the test report and maps its step tree onto the run model.

| WSJF                                                                 | TofuPilot                                                                   |
| -------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| The report (`sn`, `pn`, `result`, `start`)                           | One [Run](/docs/fundamentals/test-data-model#run).                          |
| `sn`                                                                 | [Unit](/docs/inventory/units) serial number.                                |
| `pn` / `rev`                                                         | [Part](/docs/inventory/parts) and [Revision](/docs/inventory/parts).        |
| `root.steps[]`                                                       | [Phases](/docs/fundamentals/test-data-model#phase).                         |
| `numericMeas[]` (`value`, `lowLimit`, `highLimit`, `unit`, `compOp`) | [Measurements](/docs/fundamentals/test-data-model#measurement) with limits. |
| `uut.user`                                                           | Run operator (`operated_by`).                                               |
| `result` (`P`/`F`)                                                   | Run outcome.                                                                |

## Prerequisites

* A WATS report exported as WSJF (JSON).
* A [Procedure](/docs/fundamentals/test-data-model#procedure) to attach the runs to, created in the dashboard.

## Auto-detection

TofuPilot detects WATS by the file content. A JSON file is treated as WATS when it carries the WSJF identity fields `sn`, `pn`, `result`, and `start` (or a `root.steps` tree with a `processName`). On the import page the row shows **Auto-detected** as WATS; override it from the row's detail sidebar if needed.

## Import from the dashboard

1. Open the target procedure's import page.
2. Drag your WSJF `.json` file onto the dropzone. The row shows **Auto-detected** as WATS and a **Ready** status.
3. Click **Import**. The run appears under the procedure.

Drop multiple reports at once to import a batch; each is parsed independently.

## Import through the API

Upload the file, then import it by `upload_id` with the `WATS` importer.

```bash filename="import-wats.sh"
# Import a single WATS WSJF report (upload_id comes from the upload finalize step)
curl -X POST https://www.tofupilot.app/api/v2/import \
  -H "Authorization: Bearer $TOFUPILOT_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "items": [
      { "upload_id": "550e8400-e29b-41d4-a716-446655440000", "importer": "WATS" }
    ]
  }'
```

The endpoint returns a `{ results: [...] }` array with one entry per item. Pass several files in the `items` array (up to 100) to import them in one call; each is parsed independently and reported per-item. See the [REST API reference](/docs/api/rest-api) for the upload flow and response schema.

<Callout type="info">
  TofuPilot flags duplicate files by content hash, so re-importing the same report links to the existing run instead of creating a copy.
</Callout>
