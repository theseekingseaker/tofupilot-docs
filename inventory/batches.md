<!-- Source: https://www.tofupilot.com/docs/inventory/batches -->
# Batches
URL: /docs/inventory/batches

Learn how to group units made together with TofuPilot Batches and slice yield by manufacturing lot, build day, or contract manufacturer shipment.

***

title: Batches
description: "Learn how to group units made together with TofuPilot Batches and slice yield by manufacturing lot, build day, or contract manufacturer shipment."
----------------------------------------------------------------------------------------------------------------------------------------------------------------

A batch groups [Units](/docs/inventory/units) you produced together, such as a manufacturing lot, a build day, or a contract manufacturer shipment. You can use batches to compare [yield](/docs/run-analytics) across production [Runs](/docs/fundamentals/test-data-model#run), isolate a defective lot, and correlate failures with assembly conditions.

## Identity

A batch is a thin record whose identity is its number.

| Field    | Type   | Required | Notes                                                                 |
| -------- | ------ | -------- | --------------------------------------------------------------------- |
| `number` | string | yes      | 1-60 chars, `^[a-zA-Z0-9_.:+-]+$`, unique per org (case-insensitive). |

A batch holds no other fields. Identity is the number, and everything else derives from the units linked to it.

## Lifecycle

You can create batches three ways, depending on how your workflow ingests new cohorts.

* **First Run.** Calling `runs.create` with a new `batch_number` creates the batch alongside the unit, [Part](/docs/inventory/parts), and [Revision](/docs/inventory/parts#revisions).
* **API.** Call `batches.create` to pre-allocate cohorts before production starts.
* **Dashboard.** Create from **Inventory → Batches → New Batch**.

A unit is linked to a batch by `batch_number` at creation, or you can assign it later from the unit page.

When you delete a batch, the `batch_number` on its units is unset but the units themselves are kept. Batches with zero units are auto-deleted after a unit unlink, so the list never accumulates empty batches.

## In the dashboard

Open **Inventory → Batches** to manage batches.

**List columns**: batch number, unit count, and created-at (with creator info for the user or station).

**Filters**: part, revision, created-at range, and search by batch number.

**Actions**: create a batch, edit the batch number inline, bulk add or remove units through a search-and-select dialog, and jump to the units filtered by batch.

## Limits

A few hard limits apply to batches.

* Batch number: **1-60 characters**.
* No limit on units per batch.

## API

The API exposes `batches.create`, `list`, `get`, `update`, and `delete`. See the [REST API](/docs/api/rest-api) reference for filtering units by batch.

```python title="create_batch.py"
from tofupilot.v2 import TofuPilot

client = TofuPilot()

client.batches.create(number="BATCH-2026-04-12")
client.units.create(
    serial_number="SN-200",
    part_number="PCB-V1",
    batch_number="BATCH-2026-04-12",
)
client.units.list(batch_numbers=["BATCH-2026-04-12"])
```
