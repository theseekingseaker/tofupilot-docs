<!-- Source: https://www.tofupilot.com/docs/run-analytics/run-explorer -->
# Run Explorer
URL: /docs/run-analytics/run-explorer

Filter, search, and drill into every test run on TofuPilot from a single table, with saved views and one-click export to CSV for reports and audits.

***

title: Run Explorer
description: "Filter, search, and drill into every test run on TofuPilot from a single table, with saved views and one-click export to CSV for reports and audits."
-------------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Cards, Card } from 'fumadocs-ui/components/card';
import { ChartColumnStacked, ChartScatter, FlaskConical } from 'lucide-react';

The Run Explorer is the entry point into every [Run](/docs/fundamentals/test-data-model#run) your organization has uploaded. Filter by procedure, station, batch, outcome, or any metadata key; open a row to drill into phases, measurements, logs, and attachments.

Open it from **Procedures → *Procedure name* → Runs**.

## Columns

The default columns cover the core run record. You can hide, reorder, and add columns from the **Columns** menu in the toolbar.

| Column     | Purpose                                              |
| ---------- | ---------------------------------------------------- |
| Outcome    | `PASS`, `FAIL`, `ERROR`, `ABORTED`, `TIMEOUT`.       |
| Started at | Run start timestamp.                                 |
| Duration   | Wall-clock duration from start to outcome.           |
| Procedure  | Procedure name and version.                          |
| Unit       | Serial number, with sub-unit count when applicable.  |
| Station    | Station that produced the run.                       |
| Operator   | User or station key that initiated the run.          |
| Source     | `Station` for production runs, `CLI` for local runs. |
| Tags       | Free-form labels attached to the run.                |

## Filters

The filter bar combines structured filters with free-text search. Filters apply to every chart on the procedure analytics tab as well, so the explorer and the analytics dashboards stay in sync.

* **Date range.** Last 24 hours, 7 days, 30 days, custom range, or absolute interval.
* **Outcome.** Multi-select across the run outcome enum.
* **Procedure version.** Slice runs by the deployed version that produced them.
* **Station.** Limit to one or more benches.
* **Batch and revision.** Compare runs across production cohorts and design revisions.
* **Source.** Separate operator-driven runs from CLI runs.
* **Operated by.** Accounts and declared operator names in one list, combined as OR.
* **Run metadata.** Any key-value pair you attach to runs at upload time.

## Metadata

Every run carries a free-form metadata object you can write at upload time through the [SDK](/docs/sdk), the [REST API](/docs/api/rest-api), or directly from the CLI. Keys are filterable in the toolbar, exportable as columns, and matchable in [workflow triggers](/docs/integrations/workflows). See [Run metadata](/docs/fundamentals/test-data-model#run) for the schema.

## Saved views

Save a filter combination as a view so the next time you open the explorer it lands on the right slice. Views live at the organization level, so anyone on your team can open the same slice from the **Views** menu.

## Export

The **Export** button writes the current run set to a file. Pick the rows and the format from the dropdown.

* **CSV.** Flat, one row per run, with columns for outcome, procedure, unit, batch, revision, duration, timestamps, operator, and docstring.
* **JSON.** Nested, one object per run with the same fields plus the raw record.
* **Visible vs selected.** Export every visible row, or check rows in the table and export only the selection.
* **Filename.** `tofupilot-runs-<timestamp>.<ext>` so multiple exports stay distinct.

Exports honor access group scope and role permissions, so you only export the rows you can see in the table.

## Permissions

The Run Explorer respects the dashboard access model:

| Role                    | What they see                                       |
| ----------------------- | --------------------------------------------------- |
| Owner, Admin, Developer | Every run in the organization.                      |
| Viewer                  | Runs from stations within their access group scope. |
| Operator                | No access.                                          |

## See also

<Cards>
  <Card icon={<ChartColumnStacked />} title="Yield" href="/docs/run-analytics/run-analytics" description="FPY, SPY, TPY, and LPY computed on the filtered run set." />

  <Card icon={<ChartScatter />} title="Measurement control" href="/docs/run-analytics/measurement-control" description="Cpk and control charts at the measurement level." />

  <Card icon={<FlaskConical />} title="Phase pareto" href="/docs/run-analytics/phase-pareto" description="Rank phases by failure count and duration to find the weak step." />
</Cards>
