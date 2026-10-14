<!-- Source: https://www.tofupilot.com/docs/import/openhtf -->
# Import OpenHTF Logs
URL: /docs/import/openhtf

Learn how to import OpenHTF JSON logs into TofuPilot with automatic format detection, turning existing test records into runs with no column mapping.

***

title: Import OpenHTF Logs
sidebarTitle: OpenHTF
description: "Learn how to import OpenHTF JSON logs into TofuPilot with automatic format detection, turning existing test records into runs with no column mapping."
--------------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Callout } from 'fumadocs-ui/components/callout';
import { DynamicCodeBlock } from '@/components/themed-dynamic-code-block';

OpenHTF is a **structured** format: TofuPilot reads its shape directly, so importing an OpenHTF JSON log needs no column mapping. Drop the file and it becomes a [Run](/docs/fundamentals/test-data-model#run) with its phases, measurements, and limits intact. The file layout is documented field by field in the [OpenHTF test record JSON reference](https://www.openhtf.com/reference/json-schema).

This is the fastest way to backfill history from an existing [OpenHTF](/docs/frameworks/openhtf) bench without re-running tests.

<FormatSpec encoding="JSON" extension=".json" detection="Auto-detected (phases array + start_time_millis)" origin="Google, 2014 (Apache 2.0, OutputToJSON callback)" multiRun="no" mapsTo="Run, phases, measurements, limits, logs, unit, sub-units, metadata" api="POST /v2/import (importer: OPENHTF)" />

## History

OpenHTF (Open Hardware Testing Framework) is an open-source Python test framework that Google open-sourced in 2014 under Apache 2.0. It is now community-maintained and widely used in electronics manufacturing. The JSON output is not a separate standard: it is OpenHTF's in-memory `TestRecord` serialized by the `OutputToJSON` output callback, so the file mirrors the framework's own data model.

## File structure vs the TofuPilot model

OpenHTF's data model is the one TofuPilot uses, so the mapping is one-to-one with no translation.

| OpenHTF JSON                                                             | TofuPilot                                                                   |
| ------------------------------------------------------------------------ | --------------------------------------------------------------------------- |
| The record (`dut_id`, `outcome`, `start_time_millis`, `end_time_millis`) | One [Run](/docs/fundamentals/test-data-model#run).                          |
| `dut_id`                                                                 | [Unit](/docs/inventory/units) serial number.                                |
| `phases[]` (`name`, `outcome`, timing)                                   | [Phases](/docs/fundamentals/test-data-model#phase).                         |
| `measurements` on a phase (`measured_value`, `validators`, `units`)      | [Measurements](/docs/fundamentals/test-data-model#measurement) with limits. |
| `metadata.operated_by`                                                   | Run operator (`operated_by`).                                               |
| `metadata`                                                               | Run metadata.                                                               |
| `outcome` (`PASS`/`FAIL`/`ERROR`)                                        | Run outcome.                                                                |

## Prerequisites

* An OpenHTF test record saved as JSON (the output of OpenHTF's JSON output callback).
* A [Procedure](/docs/fundamentals/test-data-model#procedure) to attach the runs to, created in the dashboard.

## Auto-detection

TofuPilot detects OpenHTF by the file content, not its name. A file is treated as OpenHTF when its top-level JSON carries a `phases` array and a numeric `start_time_millis`. On the import page the row shows **Auto-detected** as OpenHTF; you can override the format from the row's detail sidebar if detection picks wrong.

## Import from the dashboard

1. Open the target procedure's import page.
2. Drag your `.json` log onto the dropzone. The row shows **Auto-detected** as OpenHTF and a **Ready** status.
3. Click **Import**. The run appears under the procedure.

To import many logs at once, drop them all in the same batch. Each file is parsed independently, so a single malformed log won't fail the others.

## Import through the API

Upload the file, then import it by `upload_id` with the `OPENHTF` importer.

```bash filename="import-openhtf.sh"
# Import a single OpenHTF log (upload_id comes from the upload finalize step)
curl -X POST https://www.tofupilot.app/api/v2/import \
  -H "Authorization: Bearer $TOFUPILOT_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "items": [
      { "upload_id": "550e8400-e29b-41d4-a716-446655440000", "importer": "OPENHTF" }
    ]
  }'
```

The endpoint returns a `{ results: [...] }` array with one entry per item. Pass several files in the `items` array (up to 100) to import them in one call; each is parsed independently and reported per-item. See the [REST API reference](/docs/api/rest-api) for the upload flow and response schema.

## Sample file

To produce a sample record, attach the JSON output callback to any test:

```python filename="generate_sample.py"
# Attach the JSON output callback to write a record you can import
from openhtf.output.callbacks import json_factory

test.add_output_callbacks(
    json_factory.OutputToJSON("./sample.json", indent=2)
)
```

<Callout type="info">
  TofuPilot flags duplicate files by content hash, so re-importing the same log links to the existing run instead of creating a copy.
</Callout>
