<!-- Source: https://www.tofupilot.com/docs/run-analytics/unit-explorer -->
# Unit Explorer
URL: /docs/run-analytics/unit-explorer

Filter, search, and inspect every physical unit in your TofuPilot inventory, with serial-number search, metadata filters, and lifetime traceability.

***

title: Unit Explorer
description: "Filter, search, and inspect every physical unit in your TofuPilot inventory, with serial-number search, metadata filters, and lifetime traceability."
-------------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Cards, Card } from 'fumadocs-ui/components/card';
import { ScanQrCode, Package, Component } from 'lucide-react';

The Unit Explorer is the entry point into every [Unit](/docs/inventory/units) you have ever produced. Filter by part number, revision, batch, status, or any unit-metadata key; open a row to see the unit's lifetime: every run, every attempt, every measurement.

Open it from **Inventory → Units**.

## Columns

The default columns cover the core unit record. You can hide, reorder, and add columns from the **Columns** menu in the toolbar.

| Column        | Purpose                                                |
| ------------- | ------------------------------------------------------ |
| Serial number | Unique identifier. Click to open the unit detail page. |
| Part          | Part number and short name.                            |
| Revision      | Hardware revision the unit was produced against.       |
| Batch         | Production batch the unit belongs to.                  |
| Status        | Latest run outcome, with attempt count when retested.  |
| Last run      | Timestamp of the most recent run on the unit.          |
| Sample        | `Golden`, `Failing`, or empty for production units.    |
| Tags          | Free-form labels attached to the unit.                 |

## Filters

The filter bar combines structured filters with free-text search across serial numbers, part numbers, and metadata values.

* **Part number and revision.** Compare units across redesigns.
* **Batch.** Slice a single production cohort.
* **Status.** Filter to passing, failing, or never-tested units.
* **Sample type.** Include or exclude golden and failing reference samples.
* **Date range.** Filter by first run, last run, or unit creation date.
* **Unit metadata.** Any key-value pair you attach to units at creation or via the API.

## Metadata

Every unit carries a free-form metadata object you can write at creation through the [SDK](/docs/sdk), the [REST API](/docs/api/rest-api), or the CLI. Keys are filterable in the toolbar, exportable as columns, and matchable in [workflow triggers](/docs/integrations/workflows). See [Unit metadata](/docs/fundamentals/device-data-model#unit) for the schema.

## Saved views

Save a filter combination as a view so the next time you open the explorer it lands on the right slice. Views live at the organization level, so anyone on your team can open the same slice from the **Views** menu.

## Export

The **Export** button writes the current unit set to a file. Pick the rows and the format from the dropdown.

* **CSV.** Flat, one row per unit, with columns for serial number, part, revision, batch, status, last run, sample type, and metadata.
* **JSON.** Nested, one object per unit with the same fields plus the raw record.
* **Visible vs selected.** Export every visible row, or check rows in the table and export only the selection.
* **Filename.** `tofupilot-units-<timestamp>.<ext>` so multiple exports stay distinct.

Exports honor access group scope and role permissions, so you only export the rows you can see in the table.

## Permissions

The Unit Explorer respects the dashboard access model:

| Role                    | What they see                                             |
| ----------------------- | --------------------------------------------------------- |
| Owner, Admin, Developer | Every unit in the organization.                           |
| Viewer                  | Units tested on stations within their access group scope. |
| Operator                | No access.                                                |

## See also

<Cards>
  <Card icon={<ScanQrCode />} title="Units" href="/docs/inventory/units" description="The unit record reference: serial numbers, metadata, sub-units, samples." />

  <Card icon={<Component />} title="Parts and revisions" href="/docs/inventory/parts" description="Group units under product families and design revisions." />

  <Card icon={<Package />} title="Batches" href="/docs/inventory/batches" description="Group units made together and slice yield by manufacturing lot." />
</Cards>
