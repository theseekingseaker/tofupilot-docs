<!-- Source: https://www.tofupilot.com/docs/import/wsxf -->
# Import WATS WSXF Reports
URL: /docs/import/wsxf

Learn how to import Virinco WATS reports in WSXF (XML) format into TofuPilot with automatic detection, the XML companion to the WSJF JSON format.

***

title: Import WATS WSXF Reports
sidebarTitle: WSXF
description: "Learn how to import Virinco WATS reports in WSXF (XML) format into TofuPilot with automatic detection, the XML companion to the WSJF JSON format."
----------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Callout } from 'fumadocs-ui/components/callout';

WSXF (WATS Standard XML Format) is the XML companion to [WSJF](/docs/import/wsjf), Virinco WATS's JSON format. It is a **structured** format: TofuPilot reads the XML directly, so importing a WSXF report needs no column mapping. The report's step tree becomes a TofuPilot [Run](/docs/fundamentals/test-data-model#run).

Use this when your WATS export is XML rather than JSON.

<FormatSpec encoding="XML" extension=".xml" detection="Auto-detected (XML root + WATS schema)" origin="Virinco AS, WATS Standard XML Format (XSD-validated)" multiRun="yes" mapsTo="Run, phases, measurements, limits, unit, part, revision" api="POST /v2/import (importer: WSXF)" />

## History

WSXF is the original WATS interchange format defined by Virinco AS, specified as XML with an XSD schema. The newer [WSJF](/docs/import/wsjf) (JSON) carries the same data model and was added to simplify third-party integration. Both remain current; the WATS Client auto-imports either from its standard-format folders.

## File structure vs the TofuPilot model

WSXF expresses the same UUT/UUR data model as [WSJF](/docs/import/wsjf), as XSD-validated XML. One document may hold several reports. TofuPilot imports each test report and maps its step tree onto the run model, exactly as for WSJF.

| WSXF                                            | TofuPilot                                                                   |
| ----------------------------------------------- | --------------------------------------------------------------------------- |
| Report element (serial, part, revision, result) | One [Run](/docs/fundamentals/test-data-model#run).                          |
| Serial number                                   | [Unit](/docs/inventory/units) serial number.                                |
| Part number / revision                          | [Part](/docs/inventory/parts) and [Revision](/docs/inventory/parts).        |
| Step tree                                       | [Phases](/docs/fundamentals/test-data-model#phase).                         |
| Numeric step (value, limits, unit)              | [Measurements](/docs/fundamentals/test-data-model#measurement) with limits. |
| `UUT` > `UserLoginName`                         | Run operator (`operated_by`).                                               |
| Report result                                   | Run outcome.                                                                |

## Prerequisites

* A WATS report exported as WSXF (`.xml`). The schema may carry multiple reports in one document; TofuPilot imports each as its own run.
* A [Procedure](/docs/fundamentals/test-data-model#procedure) to attach the runs to, created in the dashboard.

## Auto-detection

TofuPilot detects WSXF by its XML root and WATS schema, not by file name. On the import page the row shows **Auto-detected** as WSXF; override the format from the row's detail sidebar if needed.

## Import from the dashboard

1. Open the target procedure's import page.
2. Drag your WSXF `.xml` file onto the dropzone. The row shows **Auto-detected** as WSXF and a **Ready** status.
3. Click **Import**. The runs appear under the procedure.

A WSXF document with multiple reports imports as multiple runs in one action.

## Import through the API

Upload the file, then import it by `upload_id` with the `WSXF` importer.

```bash filename="import-wsxf.sh"
# Import a WATS WSXF report (upload_id comes from the upload finalize step)
curl -X POST https://www.tofupilot.app/api/v2/import \
  -H "Authorization: Bearer $TOFUPILOT_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "items": [
      { "upload_id": "550e8400-e29b-41d4-a716-446655440000", "importer": "WSXF" }
    ]
  }'
```

The endpoint returns a `{ results: [...] }` array with one entry per item; a multi-report document reports its run ids in the item's `ids` array. Pass several files in the `items` array (up to 100) to import them in one call; each is parsed independently and reported per-item. See the [REST API reference](/docs/api/rest-api) for the upload flow and response schema.

## Format

WSXF is the XML form of a Virinco WATS report. For the JSON equivalent, see [WSJF](/docs/import/wsjf).

<Callout type="info">
  TofuPilot flags duplicate files by content hash, so re-importing the same report links to the existing run instead of creating a copy.
</Callout>
