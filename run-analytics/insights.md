<!-- Source: https://www.tofupilot.com/docs/run-analytics/insights -->
# Insights
URL: /docs/run-analytics/insights

Learn how to author saved visualizations of run, unit, and measurement data with TofuPilot Insights, the building blocks of every analytics report.

***

title: Insights
description: "Learn how to author saved visualizations of run, unit, and measurement data with TofuPilot Insights, the building blocks of every analytics report."
preview: true
-------------

import { Callout } from 'fumadocs-ui/components/callout';

An insight is a saved visualization (a chart, a KPI, or a table) and the building block of every [report](/docs/run-analytics/reports). Each insight is a self-contained query over [Runs](/docs/fundamentals/test-data-model#run), [Units](/docs/inventory/units), [Phases](/docs/fundamentals/test-data-model#phase), and [Measurements](/docs/fundamentals/test-data-model#measurement), with the same filter vocabulary you already use on the [analytics](/docs/run-analytics) tab.

## Identity

Every insight is described by the same set of fields, so the schema stays predictable across kinds.

| Field         | Type      | Required | Notes                                                                                  |
| ------------- | --------- | -------- | -------------------------------------------------------------------------------------- |
| `id`          | uuid      | yes      | Stable identifier. Used when you attach the insight to a report.                       |
| `name`        | string    | yes      | 1-60 chars.                                                                            |
| `description` | string    | no       | Up to 50000 chars. Rendered on the insight detail page.                                |
| `type`        | enum      | yes      | One of the eight insight kinds. Denormalized from `config.kind`.                       |
| `config`      | json      | yes      | Discriminated union by `type`. Holds metric, filters, group-by, and target.            |
| `sources`     | string\[] | yes      | Auto-extracted from `config`. The tables backing the insight, such as `run` or `unit`. |

## Insight types

You can pick from eight kinds when you author an insight, and each one is tailored to a specific question.

| Kind          | Shape                                                                   |
| ------------- | ----------------------------------------------------------------------- |
| `scalar`      | Single KPI value, with an optional comparison to the previous period.   |
| `trends`      | Line or bar chart over time, with optional group-by and segment-by.     |
| `table`       | Tabular data with sortable columns and a row limit.                     |
| `histogram`   | Distribution of a numeric measurement, with optional LSL and USL lines. |
| `pareto`      | Ranked categories with cumulative percentage.                           |
| `capability`  | Cpk, Cp, and Ppk for a measurement against spec limits.                 |
| `yield_gauge` | Radial gauges for FPY, second-attempt, third-attempt, and final yield.  |
| `yield_trend` | Stacked bar of yield-by-attempt over time.                              |

Each kind has its own config schema. For example, `trends` requires `xAxis`, `yAxis`, and `granularity`, while `capability` requires `lsl` and `usl`. The editor guides you through the right fields for the kind you pick.

## Aggregations

You can apply a wide range of aggregations to numeric data, grouped by family.

| Group       | Functions                                                |
| ----------- | -------------------------------------------------------- |
| Basic       | `sum`, `avg`, `count`, `min`, `max`, `none`              |
| Yield       | `yield`, `dpmo`                                          |
| Capability  | `cpk`, `cp`, `ppk`                                       |
| Spread      | `stddev`, `variance`, `range`                            |
| Percentiles | `median`, `p5`, `p10`, `p25`, `p75`, `p90`, `p95`, `p99` |

These apply to `scalar`, `trends`, `pareto`, and any `table` column that resolves to a numeric field.

## Filters

Every insight config carries a `filters` array, and each filter is a field, operator, and value triple evaluated against the source. The syntax matches what you use on the analytics tab.

When a report carries its own filters, they intersect with the filters on each insight and never widen the result.

## Preview vs execute

An insight is queried in two modes, depending on whether you are still authoring it or rendering it for a viewer.

| Operation   | When                                                   | Returns                                                             |
| ----------- | ------------------------------------------------------ | ------------------------------------------------------------------- |
| **Preview** | At author time, before you save. Driven by the editor. | The query result plus a debug payload that includes execution time. |
| **Execute** | At read time, when the insight renders.                | The query result only.                                              |

Preview accepts a raw `config`, while execute looks the config up by insight `id`. Both honor [access group scoping](/docs/access/groups).

## Author an insight

Follow these steps to add your first insight to a report.

1. Open **Analytics → Reports → New report**. Insights are authored inside a report.
2. Click **Add insight** to open the template selector.
3. Pick a template or start from scratch.
4. Configure the metric, filters, group-by, and target. The preview re-runs as you type.
5. Name the insight and save it.

<Callout type="info">
  Name the insight after the question it answers (`FPY by station, last 30 days`), not the chart type.
</Callout>

## Reuse across reports

An insight is an organization-level resource, so you can attach it to as many reports as you want. Edits propagate to every report that uses the insight, and deletion removes it everywhere at once.

## Field usage tracking

On save, TofuPilot extracts every field reference and writes a row to `insight_field_usage`. This table is used internally to answer "which insights break if I drop measurement X?" before schema changes, and it is not exposed in the dashboard.

## In the dashboard

Open **Analytics → Reports → \<your report> → Add insight** to author or edit an insight. The settings panel exposes:

* Name and description.
* Kind picker.
* Metric and axis configuration (varies by kind).
* Filters.
* Group-by and segment-by (where applicable).
* Target line or threshold (where applicable).
* Display overrides at the report-card level.

The card actions menu exposes **Edit**, **Duplicate**, and **Remove from report**.

## Limits

A few hard limits apply to insight configuration.

* Name: 1-60 characters.
* Description: 50000 characters.
* `table` row limit: 1-1000, default 100.
* `histogram` bins: 5-100, default 20.
* `pareto` top-N: 1-50, default 10.
