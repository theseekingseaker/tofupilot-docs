<!-- Source: https://www.tofupilot.com/docs/import/atdf -->
# Import ATDF Test Data
URL: /docs/import/atdf

Learn how to import ATDF semiconductor test datalogs into TofuPilot with automatic detection, the human-readable ASCII twin of the STDF binary format.

***

title: Import ATDF Test Data
sidebarTitle: ATDF
description: "Learn how to import ATDF semiconductor test datalogs into TofuPilot with automatic detection, the human-readable ASCII twin of the STDF binary format."
---------------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Callout } from 'fumadocs-ui/components/callout';

ATDF (ASCII Test Data Format) is the text form of [STDF](/docs/import/stdf): the same semiconductor ATE records, written as readable ASCII instead of binary. It is a **structured** format, so importing an ATDF file needs no column mapping. Each tested device becomes a [Run](/docs/fundamentals/test-data-model#run).

Use this when your tester or conversion tool emits ATDF rather than binary STDF.

<FormatSpec encoding="ASCII text, one record per line" extension=".atdf" detection="Auto-detected (leading FAR:A line)" origin="Teradyne, ASCII twin of SEMI STDF V4" multiRun="yes" mapsTo="Run per part, numeric/boolean measurements, limits, unit, lot metadata" api="POST /v2/import (importer: ATDF)" />

## History

ATDF is the ASCII representation of [STDF](/docs/import/stdf): the same record set and field semantics, but text instead of binary so it opens in any editor. It tracks the same V4 lineage (Teradyne origin, SEMI-licensed spec) and exists as a debugging and interchange companion to binary STDF.

## File structure vs the TofuPilot model

ATDF carries the same records as STDF, one per line, as readable text. The first line is `FAR:A|...` (the `A` marks the file as ATDF); each line is `REC_TYPE:field|field|...` with pipes between fields, commas and slashes inside them. Because the records match STDF, the mapping is identical.

| ATDF record   | TofuPilot                                                                                                                                                   |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `PIR` / `PRR` | One [Run](/docs/fundamentals/test-data-model#run), with the part's pass/fail bin as its outcome.                                                            |
| `PTR`         | A numeric [Measurement](/docs/fundamentals/test-data-model#measurement) with low and high limits.                                                           |
| `FTR`         | A boolean measurement.                                                                                                                                      |
| `MIR`         | `PART_TYP` as the [Part](/docs/inventory/parts) and `OPER_NAM` as the run operator. `LOT_ID` and `NODE_NAM` are kept as run metadata (`lot_id`, `station`). |
| part ID       | [Unit](/docs/inventory/units) serial number.                                                                                                                |

A multi-part ATDF file imports as one run per part.

## Prerequisites

* An ATDF file (`.atdf`).
* A [Procedure](/docs/fundamentals/test-data-model#procedure) to attach the runs to, created in the dashboard.

## Auto-detection

TofuPilot detects ATDF by its leading `FAR:A` record line, not by file name. On the import page the row shows **Auto-detected** as ATDF; override the format from the row's detail sidebar if needed.

## Import from the dashboard

1. Open the target procedure's import page.
2. Drag your `.atdf` file onto the dropzone. The row shows **Auto-detected** as ATDF and a **Ready** status.
3. Click **Import**. The runs appear under the procedure.

Drop multiple files at once to import a batch; each is parsed independently.

## Import through the API

Upload the file, then import it by `upload_id` with the `ATDF` importer.

```bash filename="import-atdf.sh"
# Import an ATDF datalog (upload_id comes from the upload finalize step)
curl -X POST https://www.tofupilot.app/api/v2/import \
  -H "Authorization: Bearer $TOFUPILOT_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "items": [
      { "upload_id": "550e8400-e29b-41d4-a716-446655440000", "importer": "ATDF" }
    ]
  }'
```

The endpoint returns a `{ results: [...] }` array with one entry per item. Pass several files in the `items` array (up to 100) to import them in one call; each is parsed independently and reported per-item. See the [REST API reference](/docs/api/rest-api) for the upload flow and response schema.

## Format

ATDF is the human-readable ASCII twin of STDF. Its first lines show the record layout:

```text filename="sample.atdf"
FAR:A|4|2|U
MIR:TP-DEMO-1|station-a|FT|operator|JOB1
 |8:00:00 16-JAN-2025|8:00:12 16-JAN-2025|Demo
PTR:1000|1|P|0|3.31|supply_voltage|V||||3.2|3.4
```

<Callout type="info">
  TofuPilot flags duplicate files by content hash, so re-importing the same datalog links to the existing runs instead of creating copies.
</Callout>
