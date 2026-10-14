<!-- Source: https://www.tofupilot.com/docs/import/atml -->
# Import ATML Test Data
URL: /docs/import/atml

Learn how to import ATML XML test reports into TofuPilot with automatic detection, the IEEE standard for mil/aero and ATE unit-under-test results.

***

title: Import ATML Test Data
sidebarTitle: ATML
description: "Learn how to import ATML XML test reports into TofuPilot with automatic detection, the IEEE standard for mil/aero and ATE unit-under-test results."
-----------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Callout } from 'fumadocs-ui/components/callout';

ATML (Automatic Test Markup Language) is an IEEE family of XML schemas for exchanging ATE test results, widely used in mil/aero and adopted across telecom, automotive, and consumer electronics. It is a **structured** format: TofuPilot reads the XML directly, so importing an ATML report needs no column mapping. Each unit-under-test result becomes a [Run](/docs/fundamentals/test-data-model#run); a document holding several results imports as several runs.

<FormatSpec encoding="XML" extension=".xml" detection="Auto-detected (XML root + ATML namespace)" origin="IEEE SCC20, IEEE 1671-2010 (TestResults 2.02, 5.00, 6.01)" multiRun="yes" mapsTo="Run, phases, measurements, limits, unit, outcomes" api="POST /v2/import (importer: ATML)" />

## History

ATML is developed by IEEE Standards Coordinating Committee 20 (SCC20), led by the U.S. Naval Air Systems Command with ATE industry members, to replace proprietary, non-interoperable test-data formats. The base standard is IEEE 1671-2010 (mirrored internationally as IEC 61671-2012). Adoption is driven mainly by mil/aero; NI ships a TestStand ATML Toolkit as one commercial implementation.

## Supported versions

TofuPilot imports the ATML **Test Results** schema (`TestResults`), a core schema in the base IEEE 1671 standard, for versions **2.02, 5.00, and 6.01**. Other ATML schemas (test descriptions, instrument capabilities) are not imported.

## File structure vs the TofuPilot model

An ATML Test Results document is a typed XML tree: a `TestResults` root holds a `ResultSet`, which nests `TestGroup` and `Test` elements down to individual outcomes and limits. A `TestResultsCollection` (5.00/6.01) may wrap several `TestResults` documents in one file. TofuPilot walks that tree onto the run model.

| ATML element                          | TofuPilot                                                                                          |
| ------------------------------------- | -------------------------------------------------------------------------------------------------- |
| Each `TestResults` / `ResultSet`      | One [Run](/docs/fundamentals/test-data-model#run) (a `TestResultsCollection` yields several runs). |
| `UUT` serial number                   | [Unit](/docs/inventory/units) serial number.                                                       |
| `TestGroup`                           | [Phases](/docs/fundamentals/test-data-model#phase).                                                |
| `Test` (value + `TestLimit` Low/High) | [Measurements](/docs/fundamentals/test-data-model#measurement) with limits.                        |
| `Personnel` / `SystemOperator`        | Run operator (`operated_by`).                                                                      |
| `Outcome`                             | Run, phase, and measurement outcomes.                                                              |

## Prerequisites

* An ATML Test Results report (`.xml`).
* A [Procedure](/docs/fundamentals/test-data-model#procedure) to attach the runs to, created in the dashboard.

## Auto-detection

TofuPilot detects ATML by its XML root and the ATML namespace, not by file name. On the import page the row shows **Auto-detected** as ATML; override the format from the row's detail sidebar if needed.

## Import from the dashboard

1. Open the target procedure's import page.
2. Drag your `.xml` report onto the dropzone. The row shows **Auto-detected** as ATML and a **Ready** status.
3. Click **Import**. The runs appear under the procedure (a `TestResultsCollection` imports as several runs in one action).

Drop multiple reports at once to import a batch; each is parsed independently.

## Import through the API

Upload the file, then import it by `upload_id` with the `ATML` importer.

```bash filename="import-atml.sh"
# Import an ATML Test Results report (upload_id comes from the upload finalize step)
curl -X POST https://www.tofupilot.app/api/v2/import \
  -H "Authorization: Bearer $TOFUPILOT_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "items": [
      { "upload_id": "550e8400-e29b-41d4-a716-446655440000", "importer": "ATML" }
    ]
  }'
```

The endpoint returns a `{ results: [...] }` array with one entry per item; a file that produces multiple runs reports their ids in the item's `ids` array. Pass several files in the `items` array (up to 100) to import them in one call; each is parsed independently and reported per-item. See the [REST API reference](/docs/api/rest-api) for the upload flow and response schema.

<Callout type="info">
  TofuPilot flags duplicate files by content hash, so re-importing the same report links to the existing run instead of creating a copy.
</Callout>
