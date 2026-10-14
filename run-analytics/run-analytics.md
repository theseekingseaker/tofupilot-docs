<!-- Source: https://www.tofupilot.com/docs/run-analytics/run-analytics -->
# Run Analytics
URL: /docs/run-analytics/run-analytics

Learn how TofuPilot computes first-pass, second-pass, third-pass, and last-pass yield per unit per day from every run you upload.

***

title: Run Analytics
description: "Learn how TofuPilot computes first-pass, second-pass, third-pass, and last-pass yield per unit per day from every run you upload."
------------------------------------------------------------------------------------------------------------------------------------------------

import { Callout } from 'fumadocs-ui/components/callout';
import { Cards, Card } from 'fumadocs-ui/components/card';
import { ChartScatter, FileChartColumn, Check } from 'lucide-react';

TofuPilot computes four yield metrics on every uploaded [Run](/docs/fundamentals/test-data-model#run) and exposes them on the [analytics](/docs/run-analytics) tab, so you can track quality and rework load from a single view.

## The four yields

Each metric counts a different attempt against the same unit.

| Metric                      | Counts                                                                    |
| --------------------------- | ------------------------------------------------------------------------- |
| **First-Pass Yield (FPY)**  | [Units](/docs/inventory/units) that pass on the first attempt of the day. |
| **Second-Pass Yield (SPY)** | Units that pass on the second attempt.                                    |
| **Third-Pass Yield (TPY)**  | Units that pass on the third attempt.                                     |
| **Last-Pass Yield (LPY)**   | Units that eventually pass within the window. Scrap rate is `1 - LPY`.    |

## How yield is computed

Yield is calculated per [Unit](/docs/inventory/units) per day, indexed by attempt, and three rules govern the count.

* Only the first run of each attempt index counts. A unit you retest four times produces one FPY, one SPY, one TPY, and one LPY data point if it passes by attempt four.
* [Reference samples](/docs/fundamentals/device-data-model#unit) flagged `golden` or `failing` are excluded by default.
* `ABORTED` runs do not count, while `ERROR` and `TIMEOUT` runs count as not-passing.

The same rules apply on every chart, table, report, and CSV export.

## Reading the chart

The yield chart plots FPY, SPY, TPY, and LPY as four lines over the selected range, and for any given day the relationship is FPY ≤ SPY ≤ TPY ≤ LPY.

<Callout type="info">
  Filter to **Station only** in the Source filter to exclude [CLI](/docs/cli) runs from production yield.
</Callout>

## Slicing yield

You can apply filters from the filter bar to compare yield across the dimensions that matter to your line.

* **Procedure version**: compare FPY before and after a [Deployment](/docs/deployments).
* **[Revision](/docs/inventory/parts#revisions)**: track yield by revision across redesigns.
* **[Batch](/docs/inventory/batches)**: track yield by production cohort.
* **Station**: compare yield across benches.
* **Source**: separate operator runs from automated CI runs.

## Exporting yield data

Every chart exports as CSV from the overflow menu, with columns for date, attempt index, count, and percentage.

For programmatic access, `GET /v2/runs` exposes the underlying data with the same filters you use on the dashboard.

<Cards>
  <Card icon={<ChartScatter />} title="Measurement control" href="/docs/run-analytics/measurement-control" description="Phase-level Cpk, control charts, and rankings." />

  <Card icon={<FileChartColumn />} title="Reports" href="/docs/run-analytics/reports" description="Save a yield snapshot for status updates." />

  <Card icon={<Check />} title="Runs" href="/docs/fundamentals/test-data-model#run" description="How runs are recorded and link to units." />
</Cards>
