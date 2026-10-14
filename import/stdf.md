<!-- Source: https://www.tofupilot.com/docs/import/stdf -->
# Import STDF Test Data
URL: /docs/import/stdf

Learn how to import STDF semiconductor ATE datalogs into TofuPilot with automatic format detection, turning Teradyne and Advantest output into runs.

***

title: Import STDF Test Data
sidebarTitle: STDF
description: "Learn how to import STDF semiconductor ATE datalogs into TofuPilot with automatic format detection, turning Teradyne and Advantest output into runs."
-------------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Callout } from 'fumadocs-ui/components/callout';

STDF (Standard Test Data Format) is the de-facto datalog format for semiconductor [ATE](/docs/import). It is a **structured** format: TofuPilot reads its records directly, so importing an STDF file needs no column mapping. Each tested device becomes a [Run](/docs/fundamentals/test-data-model#run) with its tests and limits intact.

Use this to pull data straight off Teradyne, Advantest, Cohu, or SPEA testers without re-running anything.

<FormatSpec encoding="Binary, record-based" extension=".stdf" detection="Auto-detected (FAR record header)" origin="Teradyne, 1985 (SEMI STDF V4)" multiRun="yes" mapsTo="Run per part, numeric/boolean measurements, limits, unit, lot metadata" api="POST /v2/import (importer: STDF)" />

## History

Teradyne's Semiconductor CIM Division developed STDF in 1985 as a vendor-independent way to log semiconductor ATE test data, originally on UNIX testers. Refined with input from six ATE vendors and 22 customers, it became the de-facto industry datalog standard. The current release is Version 4 (V4-2007); SEMI distributes the spec under license.

## File structure vs the TofuPilot model

STDF is a binary, record-based format. A file opens with a `FAR` record and closes with an `MRR`, and every record starts with a 4-byte header (`REC_LEN`, `REC_TYP`, `REC_SUB`). TofuPilot reads the part and test records and maps them onto the run model.

| STDF record                       | TofuPilot                                                                                                                                                   |
| --------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `PIR` / `PRR` (part start/result) | One [Run](/docs/fundamentals/test-data-model#run), with the part's pass/fail bin as its outcome.                                                            |
| `PTR` (parametric test)           | A numeric [Measurement](/docs/fundamentals/test-data-model#measurement) with its low and high limits.                                                       |
| `FTR` (functional test)           | A boolean measurement.                                                                                                                                      |
| `MIR` (master info)               | `PART_TYP` as the [Part](/docs/inventory/parts) and `OPER_NAM` as the run operator. `LOT_ID` and `NODE_NAM` are kept as run metadata (`lot_id`, `station`). |
| part ID / X-Y coords              | [Unit](/docs/inventory/units) serial number.                                                                                                                |

A multi-part STDF file imports as one run per part, so a full wafer or lot lands as a batch of runs in a single import.

## Prerequisites

* An STDF v4 datalog file (`.stdf`).
* A [Procedure](/docs/fundamentals/test-data-model#procedure) to attach the runs to, created in the dashboard.

## Auto-detection

TofuPilot detects STDF by its binary record header (the `FAR` record at the start of every v4 file), not by file name. On the import page the row shows **Auto-detected** as STDF; override the format from the row's detail sidebar if detection picks wrong.

## Import from the dashboard

1. Open the target procedure's import page.
2. Drag your `.stdf` file onto the dropzone. The row shows **Auto-detected** as STDF and a **Ready** status.
3. Click **Import**. The runs appear under the procedure.

Drop multiple datalogs at once to import a batch; each is parsed independently, so a single corrupt file won't fail the others.

## Import through the API

Upload the file, then import it by `upload_id` with the `STDF` importer.

```bash filename="import-stdf.sh"
# Import an STDF datalog (upload_id comes from the upload finalize step)
curl -X POST https://www.tofupilot.app/api/v2/import \
  -H "Authorization: Bearer $TOFUPILOT_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "items": [
      { "upload_id": "550e8400-e29b-41d4-a716-446655440000", "importer": "STDF" }
    ]
  }'
```

The endpoint returns a `{ results: [...] }` array with one entry per item. Pass several files in the `items` array (up to 100) to import them in one call; each is parsed independently and reported per-item. See the [REST API reference](/docs/api/rest-api) for the upload flow and response schema.

## Format

STDF is a binary V4 datalog. For a human-readable equivalent, see [ATDF](/docs/import/atdf).

<Callout type="info">
  TofuPilot flags duplicate files by content hash, so re-importing the same datalog links to the existing runs instead of creating copies.
</Callout>
