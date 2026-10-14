<!-- Source: https://www.tofupilot.com/docs/run-analytics/measurement-control -->
# Measurement Control
URL: /docs/run-analytics/measurement-control

Learn how to detect drift, instability, and bottlenecks at the phase level with TofuPilot Measurement Control: Cpk, control charts, and rankings.

***

title: Measurement Control
description: "Learn how to detect drift, instability, and bottlenecks at the phase level with TofuPilot Measurement Control: Cpk, control charts, and rankings."
----------------------------------------------------------------------------------------------------------------------------------------------------------------

import { DynamicCodeBlock } from '@/components/themed-dynamic-code-block';
import { Callout } from 'fumadocs-ui/components/callout';
import { Cards, Card } from 'fumadocs-ui/components/card';
import { ChartColumnStacked, FileChartColumn, DraftingCompass } from 'lucide-react';

[Yield](/docs/run-analytics/run-analytics) summarizes the outcome of a [Run](/docs/fundamentals/test-data-model#run), while measurement control reaches inside the run to expose every [Measurement](/docs/fundamentals/test-data-model#measurement) and every limit at the phase level.

You get two views: Cpk and control charts. For phase-level failure counts and durations, see [Phase Pareto](/docs/run-analytics/phase-pareto).

## Process Capability Index (Cpk)

Cpk measures how consistently a measurement stays inside its limits. It applies to numeric measurements with at least one limit, and TofuPilot recomputes it in real time as new runs land.

### Formula

The Cpk formula uses the mean and standard deviation of the selected runs.

<DynamicCodeBlock lang="plaintext" code="Cpk = min[(upper_limit - μ) / (3σ), (μ - lower_limit) / (3σ)]" />

Here, **μ** is the mean across the selected runs and **σ** is the standard deviation.

### Interpretation

You can read Cpk against the same thresholds the industry uses.

| Cpk         | Meaning                                                |
| ----------- | ------------------------------------------------------ |
| **> 1.33**  | Well-centered and stable.                              |
| **≈ 1.00**  | Marginal. A small drift will start producing failures. |
| **\< 1.00** | Not capable. The failure rate is unacceptable.         |

### Capability table

The **Capability** table lists every numeric measurement on every phase, sorted by Cpk. Sort ascending to surface the weakest ones first.

Click a row to see Cpk over time. The view shows daily Cpk values for the lower and upper limits separately.

## Control charts

A control chart plots a single measurement over time alongside its limits, mean, and ±6σ band, so you can spot drift at a glance.

| Measurement type | Chart                                                                                                         |
| ---------------- | ------------------------------------------------------------------------------------------------------------- |
| **Numeric**      | Values, lower and upper limits, mean, ±6σ band, histogram with a Gaussian overlay.                            |
| **Boolean**      | Expected values in green. Unexpected values in pink.                                                          |
| **String**       | Expected values in green. Unexpected values in pink. When no validators are set, every value renders in gray. |

For numeric measurements, if the [Procedure](/docs/fundamentals/test-data-model#procedure) changes limits over time, the chart reflects the history with day-by-day limit curves.

## Filters

Every measurement control view shares the same filter bar.

* **Date range**: pick a preset or a custom window.
* **Source**: choose user runs from [CLI](/docs/cli) or production runs from a [Station](/docs/station).
* **[Batch](/docs/inventory/batches), procedure version, part name, and revision**: slice the data the same way you would on the yield view.
* **Phase and measurement**: drill into one of either to render a control chart or a Cpk trend.

<Cards>
  <Card icon={<ChartColumnStacked />} title="Run Analytics" href="/docs/run-analytics/run-analytics" description="FPY/SPY/TPY/LPY across procedures and stations." />

  <Card icon={<FileChartColumn />} title="Reports" href="/docs/run-analytics/reports" description="Save a Cpk trend or control chart for a status update." />

  <Card icon={<DraftingCompass />} title="Measurements" href="/docs/fundamentals/test-data-model#measurement" description="Types, validators, and how Cpk is computed." />
</Cards>
