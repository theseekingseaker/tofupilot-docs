<!-- Source: https://www.tofupilot.com/docs/import/csv -->
# Import CSV Test Data
URL: /docs/import/csv

Learn how to import CSV test results into TofuPilot by mapping your columns to runs, units, phases, and measurements, then saving the mapping as a preset.

***

title: Import CSV Test Data
sidebarTitle: CSV
description: "Learn how to import CSV test results into TofuPilot by mapping your columns to runs, units, phases, and measurements, then saving the mapping as a preset."
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Callout } from 'fumadocs-ui/components/callout';

CSV is a **tabular** format: your column names are arbitrary, so you tell TofuPilot which column feeds each field. That column mapping *is* the schema. Save it once as a preset and every later file with the same layout imports in one click.

<FormatSpec encoding="Comma-separated text (RFC 4180)" extension=".csv" detection="Needs mapping (you map columns to fields)" origin="Generic tabular export" multiRun="no" mapsTo="Run, phases, measurements, limits, unit, custom metadata" api="Dashboard import (column mapping UI)" />

## Prerequisites

* A CSV file with a header row.
* A [Procedure](/docs/fundamentals/test-data-model#procedure) to attach the runs to, created in the dashboard. The procedure is chosen on the import page and always overrides any procedure named in the file.

## How tabular import works

Unlike OpenHTF or WATS, a CSV has no fixed shape. TofuPilot inspects the uploaded file's headers, then you map each TofuPilot field to one of your columns. The import page pre-selects a saved preset when your headers match it.

1. **Upload.** Drop the `.csv` on the import page. The row shows **Needs mapping**.
2. **Inspect.** Click the row to open the mapping sidebar. TofuPilot reads your headers and a sample of rows to preview the mapping.
3. **Map or pick a preset.** Apply a saved preset, or map columns by hand.
4. **Import.** The row turns to **Mapped**; click **Import** to create the runs.

## Field mapping

Map your columns to these run fields. Only **serial number**, **outcome**, **started at**, and **ended at** are required.

| TofuPilot field     | Required | Description                                                                                                                                                                 |
| ------------------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Serial number**   | Yes      | Identifies the [Unit](/docs/inventory/units) under test. Auto-creates the unit on first sight.                                                                              |
| **Outcome**         | Yes      | Run pass/fail. Use a value map to translate your labels (for example `Passed` → `PASS`).                                                                                    |
| **Started at**      | Yes      | Run start timestamp.                                                                                                                                                        |
| **Ended at**        | Yes      | Run end timestamp.                                                                                                                                                          |
| **Part number**     | No       | [Part](/docs/inventory/parts) the unit belongs to.                                                                                                                          |
| **Revision number** | No       | Design [Revision](/docs/inventory/parts) of the part.                                                                                                                       |
| **Batch number**    | No       | Production [Batch](/docs/inventory/batches).                                                                                                                                |
| **Operated by**     | No       | Operator who ran the test. An email matching a member of your organization links the run to that account. Any other value is recorded verbatim as a declared operator name. |
| **Description**     | No       | Free-text run note.                                                                                                                                                         |

### Outcome value maps

Your file rarely uses TofuPilot's exact outcome labels. A **value map** translates each source value to a canonical outcome, so `Passed`, `OK`, and `1` can all map to `PASS`. The same applies to phase outcomes.

## Measurements

A CSV stores measurements in one of two layouts. Pick the one that matches your file.

| Layout   | Shape                                                           | Map                                                                                      |
| -------- | --------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| **Long** | One row per measurement, with a name column and a value column. | Name, value, and optional units, lower limit, upper limit, outcome, and phase columns.   |
| **Wide** | One column per measurement, one row per run.                    | Each measurement column, with optional name, units, fixed lower/upper limits, and phase. |
| **None** | No measurements, just run-level pass/fail.                      | Nothing to map.                                                                          |

## Phases

For the **long** layout you can map phase data carried on the measurement rows: a **phase name** column groups measurements into [Phases](/docs/fundamentals/test-data-model#phase), with optional phase outcome, started, and ended columns. When omitted, TofuPilot derives phase outcomes from their measurements and timing from the run.

## Custom metadata

Map extra columns to typed metadata keys:

* **Run metadata** attaches the value to the run.
* **Unit metadata** attaches the value to the [Unit](/docs/inventory/units) under test (PATCH semantics, so it updates the unit).

Each side accepts up to 50 mapped columns.

## Presets

Save a finished mapping as a **preset** so the next file with the same layout maps itself. When your headers match a preset, the import page pre-selects it and the row goes straight to **Mapped**. Reuse presets across files on the import page.

## Dashboard only

Tabular import (the column-mapping flow) runs entirely on the dashboard: TofuPilot inspects the file's headers, you map columns, and the runs are created in one action. There is no public REST endpoint for tabular import, because the column mapping is a UI concept. To import CSV files, use the procedure's import page.

The public `POST /v2/import` endpoint covers the **structured** formats only (OpenHTF, WATS, ATML, NI TestStand, STDF, ATDF), whose shape the parser reads directly. See the [REST API reference](/docs/api/rest-api) for those.

<Callout type="info">
  TofuPilot flags duplicate files by content hash, so re-importing the same CSV links to the existing run instead of creating a copy.
</Callout>
