<!-- Source: https://www.tofupilot.com/docs/run-analytics -->
# Analytics
URL: /docs/run-analytics

Learn how to track yield, run volume, unit volume, and cycle timing across every procedure, station, batch, and revision with TofuPilot Analytics.

***

title: Analytics
description: "Learn how to track yield, run volume, unit volume, and cycle timing across every procedure, station, batch, and revision with TofuPilot Analytics."
-----------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Cards, Card } from 'fumadocs-ui/components/card';
import { ChartColumnStacked, ChartScatter, FileChartColumn, ChartLine } from 'lucide-react';

Every [Run](/docs/fundamentals/test-data-model#run) you upload feeds the analytics surface, which is built into the dashboard, honors the same role and access group scoping as the rest of TofuPilot, and works the same way on managed and self-hosted deployments.

<Cards>
  <Card icon={<ChartColumnStacked />} title="Yield" href="/docs/run-analytics/run-analytics" description="FPY, SPY, TPY, LPY per unit per day." />

  <Card icon={<ChartScatter />} title="Measurement control" href="/docs/run-analytics/measurement-control" description="Cpk, control charts, phase failure and duration rankings." />

  <Card icon={<FileChartColumn />} title="Reports" href="/docs/run-analytics/reports" description="Saved chart pages with filters and time range." />

  <Card icon={<ChartLine />} title="Insights" href="/docs/run-analytics/insights" description="Saved visualizations. Building blocks for reports." />
</Cards>

## What analytics covers

Analytics groups every metric into four families, so you can find what you need from a single tab.

| Family                  | Tracks                                                                      | Where                                                          |
| ----------------------- | --------------------------------------------------------------------------- | -------------------------------------------------------------- |
| **Yield**               | First-pass, second-pass, third-pass, last-pass yield per procedure per day. | [Yield](/docs/run-analytics/run-analytics)                     |
| **Volume**              | Daily run count by outcome and daily unit count.                            | This page                                                      |
| **Duration**            | Average run duration and per-phase duration trends.                         | This page + [Phase pareto](/docs/run-analytics/phase-pareto)   |
| **Measurement control** | Cpk, control charts, phase failure rankings.                                | [Measurement control](/docs/run-analytics/measurement-control) |

[Reference Samples](/docs/fundamentals/device-data-model#unit), which are [Units](/docs/inventory/units) flagged as `golden` or `failing`, are excluded by default. You can toggle them back in from the filter bar.

## Runs

The runs chart shows daily run volume grouped by outcome: `PASS`, `FAIL`, `ERROR`, `TIMEOUT`, and `ABORTED`.

## Units

The units chart shows the daily count of unique units you tested. When runs exceed units, units have been retested, and the gap is a useful proxy for rework load.

## Run duration

The run duration chart shows the daily average wall-clock duration of a run. For a per-phase breakdown, see [Phase pareto](/docs/run-analytics/phase-pareto).

## Filters

Every chart and table on the analytics tab share the same filter bar, so a single change updates the whole view.

* **Date range**: pick a preset or define a custom window.
* **Source**: choose user runs from [CLI](/docs/cli) or production runs from a [Station](/docs/station).
* **Procedure version**: compare yield before and after a [Deployment](/docs/deployments).
* **Part name and [Revision](/docs/inventory/parts#revisions)**: slice yield by revision.
* **[Batch](/docs/inventory/batches)**: slice yield by production cohort.
* **Reference Samples**: toggle golden and failing units back in.

Filters apply to every chart on the tab at once, so you never see a mixed view.

## How metrics are computed

A few rules govern how every chart in the analytics surface counts your data.

* Reference samples are excluded from yield, volume, and duration.
* `ABORTED` runs do not count toward yield.
* Yield is computed per unit per day, by attempt. A unit you test three times produces one FPY, one SPY, and one TPY data point. See [Yield](/docs/run-analytics/run-analytics) for the full breakdown.

<Cards>
  <Card icon={<ChartColumnStacked />} title="Yield" href="/docs/run-analytics/run-analytics" description="FPY, SPY, TPY, LPY in depth." />

  <Card icon={<ChartScatter />} title="Measurement control" href="/docs/run-analytics/measurement-control" description="Cpk and control charts." />

  <Card icon={<FileChartColumn />} title="Reports" href="/docs/run-analytics/reports" description="Save snapshots for status updates." />

  <Card icon={<ChartLine />} title="Insights" href="/docs/run-analytics/insights" description="Saved chart components reused inside reports." />
</Cards>
