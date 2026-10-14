<!-- Source: https://www.tofupilot.com/docs/run-analytics/phase-pareto -->
# Phase Pareto
URL: /docs/run-analytics/phase-pareto

Find the weak step in your test with TofuPilot Phase Pareto: rank phases by failures, retries, and wall-clock duration, then drill into the measurements behind each failure.

***

title: Phase Pareto
description: "Find the weak step in your test with TofuPilot Phase Pareto: rank phases by failures, retries, and wall-clock duration, then drill into the measurements behind each failure."
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Callout } from 'fumadocs-ui/components/callout';
import { Cards, Card } from 'fumadocs-ui/components/card';
import { ChartScatter, ChartColumnStacked, FileChartColumn } from 'lucide-react';

Phase pareto works at the [Phase](/docs/fundamentals/test-data-model#phase) level. It ranks phases by how often they fail, how often they retry, and how long they take, so you can target the weakest step in your test instead of guessing.

## Failures

The **Failures** view lists every phase that has failed in the selected range, sorted by failure count. Phases that have never failed are hidden.

Click a phase to surface the [Measurements](/docs/fundamentals/test-data-model#measurement) that triggered the failures, then jump into [Measurement Control](/docs/run-analytics/measurement-control) to look at the Cpk and control charts for those measurements.

## Retries

The **Retries** view ranks phases by retry attempts, surfacing the steps that churn through repeated tries before passing. Retries are counted across every attempt, not just the final one.

## Duration

The **Duration** view lists phases by average wall-clock duration, longest first.

Click a phase to see duration over time. Use it to spot regressions after a [Deployment](/docs/deployments) or to find the bottleneck in a long procedure.

## Filters

Phase pareto shares the analytics filter bar, kept in sync between the sidebar and the command bar.

* **Date range**: pick a preset or a custom window. The window scopes to when each phase ran.
* **Phase outcome**: filter by phase result, distinct from the parent run outcome.
* **Sample class**: production, golden, or failing units.
* **[Batch](/docs/inventory/batches), procedure version, part name, and revision**: slice the data the same way you would on the yield view.

<Cards>
  <Card icon={<ChartScatter />} title="Measurement control" href="/docs/run-analytics/measurement-control" description="Cpk and control charts for the measurements behind every failure." />

  <Card icon={<ChartColumnStacked />} title="Yield" href="/docs/run-analytics/run-analytics" description="FPY/SPY/TPY/LPY across procedures and stations." />

  <Card icon={<FileChartColumn />} title="Reports" href="/docs/run-analytics/reports" description="Save a failure breakdown or duration trend for a status update." />
</Cards>
