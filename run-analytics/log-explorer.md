<!-- Source: https://www.tofupilot.com/docs/run-analytics/log-explorer -->
# Log Explorer
URL: /docs/run-analytics/log-explorer

Search every log line across every run on TofuPilot, filter by level, source, run, station, or part, and jump from a log entry to the run that produced it.

***

title: Log Explorer
description: "Search every log line across every run on TofuPilot, filter by level, source, run, station, or part, and jump from a log entry to the run that produced it."
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Cards, Card } from 'fumadocs-ui/components/card';
import { Check, ScanQrCode, Logs } from 'lucide-react';

The Log Explorer surfaces every [Log](/docs/fundamentals/test-data-model#log) line your stations and CLI runs have produced. Search by message, filter by level or source, and click a row to open the run that emitted the entry.

Open it from **Procedures → *Procedure name* → Logs**, or from a specific run's **Logs** tab.

## Columns

The default columns cover the core log record. You can hide, reorder, and add columns from the **Columns** menu in the toolbar.

| Column    | Purpose                                                                                  |
| --------- | ---------------------------------------------------------------------------------------- |
| Level     | `DEBUG`, `INFO`, `WARNING`, `ERROR`, `CRITICAL`. Color-coded.                            |
| Timestamp | Absolute time the log line was emitted. Toggle to elapsed-from-run-start in the toolbar. |
| Source    | File and line number that emitted the log, when the framework captured it.               |
| Message   | The log line itself. Capped at 50,000 characters.                                        |
| Run       | Outcome and unit of the run that produced the entry.                                     |

## Filters

The filter bar combines structured filters with full-text search across the message column.

* **Level.** Multi-select across `DEBUG`, `INFO`, `WARNING`, `ERROR`, `CRITICAL`.
* **Source.** Filter to a specific file or module that emits the log.
* **Date range.** Last 24 hours, 7 days, 30 days, custom range, or absolute interval.
* **Run filters.** Procedure, station, batch, revision, and run outcome filter the underlying run set, which in turn filters the logs.
* **Free-text search.** Match across the log message body.

## Saved views

Save a filter combination as a view so the next time you open the explorer it lands on the right slice. Views live at the organization level, so anyone on your team can open the same slice from the **Views** menu.

## Export

The **Export** button writes the current log set to a file. Pick the rows and the format from the dropdown.

* **CSV.** Flat, one row per log line, with columns for level, timestamp, source, message, and the parent run ID.
* **JSON.** Nested, one object per log line with the same fields plus the raw record.
* **Visible vs selected.** Export every visible row, or check rows in the table and export only the selection.
* **Filename.** `tofupilot-logs-<timestamp>.<ext>` so multiple exports stay distinct.

Exports honor access group scope and role permissions, so you only export the rows you can see in the table.

## Permissions

The Log Explorer respects the dashboard access model:

| Role                    | What they see                                               |
| ----------------------- | ----------------------------------------------------------- |
| Owner, Admin, Developer | Every log line in the organization.                         |
| Viewer                  | Logs from runs on stations within their access group scope. |
| Operator                | No access.                                                  |

## See also

<Cards>
  <Card icon={<Check />} title="Run explorer" href="/docs/run-analytics/run-explorer" description="Browse every run and open its full report." />

  <Card icon={<ScanQrCode />} title="Unit explorer" href="/docs/run-analytics/unit-explorer" description="Trace every unit by serial number." />

  <Card icon={<Logs />} title="Log fundamentals" href="/docs/fundamentals/test-data-model#log" description="The Log data model: levels, capture rules, and limits." />
</Cards>
