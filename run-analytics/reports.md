<!-- Source: https://www.tofupilot.com/docs/run-analytics/reports -->
# Reports
URL: /docs/run-analytics/reports

Learn how to compose a dashboard from saved insights with TofuPilot Reports, including global filters, layouts, and shared time ranges.

***

title: Reports
description: "Learn how to compose a dashboard from saved insights with TofuPilot Reports, including global filters, layouts, and shared time ranges."
preview: true
-------------

import { Callout } from 'fumadocs-ui/components/callout';
import { Cards, Card } from 'fumadocs-ui/components/card';
import { ChartColumnStacked, ChartScatter, ChartLine } from 'lucide-react';

A report is a named collection of [insights](/docs/run-analytics/insights) on a single page, with optional global filters and a shared time range. You use it to assemble a status dashboard, a weekly review, or a customer-facing snapshot from reusable pieces.

## Identity

A report is described by the same set of fields whether you author it from the dashboard or the API.

| Field                  | Type   | Required | Notes                                                                                          |
| ---------------------- | ------ | -------- | ---------------------------------------------------------------------------------------------- |
| `id`                   | uuid   | yes      | Stable identifier. Used in the report URL.                                                     |
| `name`                 | string | yes      | 1-100 chars.                                                                                   |
| `description`          | string | no       | Up to 1000 chars. Rendered under the title.                                                    |
| `config.layout`        | enum   | no       | `auto`, `grid`, or `flow`.                                                                     |
| `config.timeRange`     | object | no       | Global time preset applied to insights without a pinned date range.                            |
| `config.filters`       | object | no       | Global filters: `procedureId`, `stationId`, `partNumber`. They intersect with insight filters. |
| `config.globalRefresh` | int    | no       | Auto-refresh interval in seconds, 0-3600. `0` disables auto-refresh.                           |
| `config.theme`         | enum   | no       | `light`, `dark`, or `system`.                                                                  |

Each insight on a report is a `report_insight` row that carries its own display overrides (`title`, `size`, `colorScheme`, `gridPosition`) and a `position` for ordering.

## Report Builder

The report builder is the editor at **Analytics → Reports → \<your report>**, where you manage insights and report-level settings from the same panel.

### Compose a report

Follow these steps to build a report from scratch.

1. Open **Analytics → Reports → New report**, name the report, and add an optional description.
2. Click **Add insight** to open the template selector with categorized presets or a **Custom insight** option.
3. Pick a template or build from scratch. The right panel exposes the insight config (kind, metric, filters, group-by, target).
4. Save. The insight renders as a card and becomes reusable.
5. Repeat the steps above for every insight you want on the report.

<Callout type="info">
  Insights are organization-level. Editing an insight propagates to every report that includes it.
</Callout>

### Add, remove, update insights

Each link between a report and an insight is a `report_insight` row with its own display config, so you can adjust appearance on one report without touching the underlying insight. The card actions menu exposes the following operations.

| Operation   | Effect                                                                                                                                      |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| **Add**     | Attach an existing or new insight with an optional `position`, `size`, and per-card display overrides.                                      |
| **Update**  | Change display settings on this report only: title override, color scheme, size preset, and grid position. The insight itself is unchanged. |
| **Remove**  | Detach from the report. The insight stays in the organization and on other reports.                                                         |
| **Reorder** | Drag and drop cards, or send an ordered list of `insightIds` through the API.                                                               |

The `size` presets are `small`, `medium`, `large`, `wide`, `tall`, and `full`. The `grid` layout enables an explicit `gridPosition` with `x`, `y`, `w`, and `h`.

### Global filters and time range

A report's `config` can pin a time range and a set of filters (`procedureId`, `stationId`, `partNumber`), which interact with each insight in two ways.

* **Insight filters win.** An insight that pins its own `dateRange` ignores the report's global time range.
* **Filters intersect, never widen.** A report-level `stationId` filters every insight further and cannot expose data the user could not see directly.

### Execute vs Preview

A report runs its insights in two modes, depending on whether you are authoring or viewing.

| Operation   | When                                                | Input               | Returns                                        |
| ----------- | --------------------------------------------------- | ------------------- | ---------------------------------------------- |
| **Preview** | While you author. Re-runs as you tweak the config.  | Raw `config` object | Query result plus debug info (execution time). |
| **Execute** | On every page load. Runs in parallel on the server. | Insight `id`        | Query result only.                             |

There is no caching layer in front of the executor, so each page load re-runs the queries. Set `globalRefresh` to re-run automatically at a fixed interval.

## Sharing and exporting

Reports are organization-level, and members with dashboard access who are in scope for the underlying [access group](/docs/access/groups) see the same report at the same URL.

| Action                  | How                                                              |
| ----------------------- | ---------------------------------------------------------------- |
| **Share**               | Copy the report URL.                                             |
| **Export per-chart**    | Each chart card exposes a CSV export from its overflow menu.     |
| **Programmatic access** | REST API: `report.list`, `report.get`, `report.insight.execute`. |

## Update a report

You can edit the name, description, layout, global time range, or global filters from the settings panel in the editor. The new state replaces the old one, and reports do not version themselves.

## Delete a report

Open the row actions menu and click **Delete**. Deletion is permanent, and the underlying insights are not deleted.

## Permissions

Access depends on the role of the member viewing the report.

| Role                    | Access                                                  |
| ----------------------- | ------------------------------------------------------- |
| Owner, Admin, Developer | Create, edit, and delete any report.                    |
| Viewer                  | Open and read reports for stations they have access to. |
| Operator                | No dashboard access.                                    |

A report that includes an insight filtered to a station outside a Viewer's scope returns empty for that Viewer.

## In the dashboard

Open **Analytics → Reports** to manage your reports.

**List columns**: name, description, created-by, last-updated.

**Editor**: the left rail is the insight stack, and the right panel is the selected insight's settings or the template selector when you add a new insight. The top header shows the **Saving** badge while changes propagate.

**Actions**: create a report, add an insight, remove an insight, update insight display, reorder insights, edit report settings, and delete the report.

## Limits

A few hard limits apply to report configuration.

* Name: 1-100 characters.
* Description: 1000 characters.
* Global refresh interval: 0-3600 seconds.
* Insights per report: no hard cap. Query parallelism scales linearly.
* Card size presets: `small`, `medium`, `large`, `wide`, `tall`, `full`.

<Cards>
  <Card icon={<ChartColumnStacked />} title="Yield" href="/docs/run-analytics/run-analytics" description="FPY/SPY/TPY/LPY in depth." />

  <Card icon={<ChartScatter />} title="Measurement control" href="/docs/run-analytics/measurement-control" description="Cpk, control charts, rankings." />

  <Card icon={<ChartLine />} title="Insights" href="/docs/run-analytics/insights" description="The building blocks of every report." />
</Cards>
