<!-- Source: https://www.tofupilot.com/docs/inventory/units -->
# Units
URL: /docs/inventory/units

Learn how to track units under test in TofuPilot with units: serial numbers, typed metadata, attachments, sub-unit hierarchies, and reference samples.

***

title: Units
description: "Learn how to track units under test in TofuPilot with units: serial numbers, typed metadata, attachments, sub-unit hierarchies, and reference samples."
---------------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Callout } from 'fumadocs-ui/components/callout';

A unit is a physical object you test, identified by a unique serial and linked to a [Part](/docs/inventory/parts) [Revision](/docs/inventory/parts#revisions). Each unit carries every [Run](/docs/fundamentals/test-data-model#run) you executed against it, typed metadata, [Attachments](/docs/fundamentals/test-data-model#attachment), and any sub-unit assemblies.

<Callout>
  TofuPilot uses "unit" as the canonical term. OpenHTF and other frameworks call this UUT or DUT.
</Callout>

## Identity

A unit is described by a small set of fields, most of which you can set when you upload the first run.

| Field                  | Type   | Required | Notes                                                                 |
| ---------------------- | ------ | -------- | --------------------------------------------------------------------- |
| `serial_number`        | string | yes      | 1-60 chars, `^[a-zA-Z0-9_.:+-]+$`, unique per org (case-insensitive). |
| `part_number`          | string | yes      | References an existing or auto-created [Part](/docs/inventory/parts). |
| `revision_number`      | string | no       | Defaults to `A`. Must exist on the part.                              |
| `batch_number`         | string | no       | Links to a [Batch](/docs/inventory/batches).                          |
| `parent_serial_number` | string | no       | Parent unit when this unit is a sub-assembly.                         |
| `sample`               | enum   | no       | `golden`, `failing`, or `null` (production).                          |

## Lifecycle

You can create units three ways.

* **First Run**: `runs.create` with an unknown serial creates the unit, part, revision, and batch (when a `batch_number` is sent) in one transaction. Auto-created parts get the name `New Part`.
* **API**: `units.create` is the right entry point for legacy imports, pre-allocated serials, or setting reference samples before production.
* **Dashboard**: create from **Inventory → Units → New Unit**.

Deleting a unit cascades to every run on that unit. Sub-units are not deleted, and their `parent_serial_number` is unset instead.

## Reference samples

Reference samples let you stage known-good or known-faulty hardware without polluting your yield metrics.

| Value     | Use                     | Analytics                       |
| --------- | ----------------------- | ------------------------------- |
| `golden`  | Known-good reference.   | Excluded from yield by default. |
| `failing` | Known-faulty reference. | Excluded from yield by default. |
| `null`    | Production unit.        | Included.                       |

You can toggle the **Sample** chip to include reference samples in any view.

## Sub-units

Sub-units are units assembled into a parent, and each one keeps its own serial, runs, metadata, and attachments in a tree hierarchy.

* A sub-unit has one parent at a time, and re-linking moves the unit. The API returns a warning instead of an error.
* Cycles are rejected.
* Deleting a parent unlinks its children, and the children themselves are not deleted.

Filter the units list with **Exclude Sub-Units** when you want to see only top-level assemblies.

## Metadata

Metadata follows PATCH semantics, so omitted keys are preserved and `null` deletes a key.

* Up to 50 keys per unit.
* Keys: 1-40 chars, `^[a-zA-Z0-9_.:+-]+$`.
* Values: `string` (max 50000 chars), `number`, or `boolean`. The type is auto-detected.

## Attachments

You can upload arbitrary files (calibration certificates, BOMs, photos) to a unit. Attachments are linked to the unit, not to a specific run.

```python title="attachments.py"
client.units.attachments.upload(serial_number="SN-0001", file="calibration.pdf")
client.units.attachments.delete(serial_number="SN-0001", ids=[attachment_id])
```

## In the dashboard

Open **Inventory → Units** to manage your units.

**List columns**: serial, part number, part name, revision, batch, last procedure, and last outcome. Rows are color-tinted by the last outcome.

**Filters**: part, revision (dependent on the selected parts), batch, sample, sub-unit toggle, created-by station, created-by user, procedure, run outcome, run count range, created-at range, run-date range, and latest-run-only toggle.

**Actions**: create a unit, edit the serial inline, assign a batch, set the sample type, edit metadata, add or remove parent and children, upload attachments, bulk delete, and export CSV (11 columns).

The unit detail page exposes the metadata editor, sub-unit tree, runs table, attachments, and audit activity.

## Limits

A few hard limits apply to units.

* Serial: 1-60 characters.
* Metadata: 50 keys per unit, 40 chars per key, and 50000 chars per string value.
* Sub-unit tree: no depth limit, with cycle prevention enforced.

## API

The API exposes `units.create`, `list`, `get`, `update`, `delete`, `add_child`, and `remove_child`, plus attachment operations. See the [REST API](/docs/api/rest-api) reference for schemas.

```python title="create_unit.py"
from tofupilot.v2 import TofuPilot

client = TofuPilot()

client.units.create(
    serial_number="SN-0001",
    part_number="PCB-V1",
    revision_number="A",
    batch_number="BATCH-2026-04",
)
```
