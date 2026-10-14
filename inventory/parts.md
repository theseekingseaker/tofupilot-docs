<!-- Source: https://www.tofupilot.com/docs/inventory/parts -->
# Parts and Revisions
URL: /docs/inventory/parts

Learn how to organize product families and their design versions with TofuPilot Parts and Revisions to compare yield across hardware generations.

***

title: Parts and Revisions
description: "Learn how to organize product families and their design versions with TofuPilot Parts and Revisions to compare yield across hardware generations."
----------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Callout } from 'fumadocs-ui/components/callout';

A **Part** identifies a product or component family, such as `PCB-V1`, `SENSOR-A4`, or `ASSY-4400`. A **Revision** tracks a design or process version, and every [Unit](/docs/inventory/units) belongs to one part and one revision.

## Identity

A part and its revisions share a compact set of fields.

| Field                  | Type   | Required | Notes                                                                 |
| ---------------------- | ------ | -------- | --------------------------------------------------------------------- |
| `number`               | string | yes      | 1-60 chars, `^[a-zA-Z0-9_.:+-]+$`, unique per org (case-insensitive). |
| `name`                 | string | yes      | 1-60 chars. Display name.                                             |
| `revisions[].number`   | string | yes      | 1-60 chars, unique per part.                                          |
| `revisions[].image_id` | uuid   | no       | Optional image asset for the revision (visible on the part page).     |
| `metadata`             | object | no       | Up to 50 typed keys on the part. See [Metadata](#metadata).           |
| `revisions[].metadata` | object | no       | Up to 50 typed keys on the revision. See [Metadata](#metadata).       |

## Lifecycle

Each part splits into one or more revisions, and each revision splits into the units built against it.

```
Part  ──▶  Revision A  ──▶  Units
           Revision B  ──▶  Units
           Revision C  ──▶  Units
```

You can create parts and revisions three ways.

* **First Run.** Calling `runs.create` with a new `part_number` creates the part, then the revision (which defaults to `A` if you omit it). Auto-created parts get the name `New Part` until you rename them.
* **API.** Call `parts.create` and `parts.revisions.create` to seed inventory before any [Run](/docs/fundamentals/test-data-model#run) lands.
* **Dashboard.** Create from **Inventory → Parts → New Part**, then add revisions from the part detail page.

<Callout type="warn">
  Deleting a part cascades: revisions, units on those revisions, and every run on those units are deleted. Deleting the last revision of a part deletes the part automatically.
</Callout>

## Revisions

Use a new revision when the design or process changes enough to affect [yield](/docs/run-analytics): a new BOM, new firmware, or a new assembly line. You can then filter analytics by `revision_number` to validate a fix or detect a regression.

Each revision can carry an image (a schematic, photo, or render) that you upload from the dashboard or through `PATCH /v2/parts/{number}/revisions/{revision_number}` with an `image_id`.

## Metadata

Parts and revisions each carry their own metadata. It follows PATCH semantics, so omitted keys are preserved and `null` deletes a key.

* Up to 50 keys per part and per revision.
* Keys: 1-40 chars, `^[a-zA-Z0-9_.:+-]+$`.
* Values: `string` (max 50000 chars), `number`, or `boolean`. The type is auto-detected.

## In the dashboard

Open **Inventory → Parts** to manage your parts.

**List columns**: part number, name, total units, procedures count, and revisions count. Rows expand to show revisions inline.

**Actions on the Part page**: edit the part number, edit the name, edit metadata, create a revision, delete the part, view [Procedures](/docs/fundamentals/test-data-model#procedure) linked to the part, and jump to units filtered by the part. The part and revision detail pages expose the metadata editor.

## Limits

A few hard limits apply to parts and revisions.

* Part number: **1-60 characters**.
* Name: **1-60 characters**.
* Revision number: **1-60 characters**, unique within the part.
* Metadata: **50 keys** per part and per revision, 40 chars per key, and 50000 chars per string value.

## API

The API exposes `parts.create`, `list`, `get`, `update`, and `delete`. Revisions expose `create`, `get`, `update`, and `delete`, with no standalone list because revisions are nested on `parts.get`. See the [REST API](/docs/api/rest-api) reference for schemas.

```python title="create_part.py"
from tofupilot.v2 import TofuPilot

client = TofuPilot()

client.parts.create(number="PCB-001", name="Main PCB")
client.parts.revisions.create(part_number="PCB-001", number="C")
client.units.create(serial_number="SN-100", part_number="PCB-001", revision_number="C")
```
