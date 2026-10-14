<!-- Source: https://www.tofupilot.com/docs/alerts -->
# Alerts
URL: /docs/alerts

Get notified when something's wrong with your test lines. Set up email alerts for yield drops, measurement drift, and sample failures to fix issues fast.

***

title: Alerts
description: "Get notified when something's wrong with your test lines. Set up email alerts for yield drops, measurement drift, and sample failures to fix issues fast."
------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Alerts let you know when something's wrong with your test lines, like a drop in first-pass yield or a measurement drifting on one station. You can get these alerts by email so you can respond to issues.

By default, you'll be notified about:

* **Yield drop**: When first-pass yield falls significantly below the procedure's own baseline.
* **Measurement drift**: When a measurement shifts or its capability drops against its own baseline.
* **Golden sample failed**: When a known-good reference unit fails.
* **Failing sample passed**: When a known-faulty reference unit passes.
* **Unit retest threshold**: When a unit is tested more times than is normal for the procedure.

## Alert types

TofuPilot-defined minimum activity thresholds reduce low-volume noise and are not configured in alert rules. For details, see [How alerts trigger](#how-alerts-trigger).

| Alert Type                | Triggered when                                                                                                                                                                                                                   | Grouping                 |
| ------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------ |
| **Yield drop**            | Fires when recency-weighted first-pass yield is more than 2 standard deviations below the procedure's own baseline. Custom rules can track [FPY, LPY, or run pass rate](#yield-drop-metrics) instead.                            | Procedure, station, part |
| **Measurement drift**     | Fires when a measurement series moves more than 3 standard deviations from its own baseline. Custom rules can track [Cpk drift, mean drift, or a Cpk floor](#measurement-drift-metrics). Each part is tracked as its own series. | Procedure, part          |
| **Golden sample failed**  | Fires when a unit flagged `golden` fails a run (critical) or its run does not complete (warning). A passing run resolves the alert.                                                                                              | Unit                     |
| **Failing sample passed** | Fires when a unit flagged `failing` passes a run (critical) or its run does not complete (warning). A failing run resolves the alert.                                                                                            | Unit                     |
| **Unit retest threshold** | Fires when a unit's lifetime test count on a procedure exceeds the learned retest threshold, with a floor of 3. Escalates to critical at twice the threshold.                                                                    | Unit                     |
| **Run failed**            | Fires when consecutive failed production runs reach the rule's threshold. Available in custom rules only and not enabled by default.                                                                                             | Procedure, station, part |

### Yield drop metrics

Custom yield drop rules support these metrics, measured as a drop in percentage points against the preceding baseline:

* [First Pass Yield](/docs/run-analytics/run-analytics): units passing on their first-ever run.
* [Last Pass Yield](/docs/run-analytics/run-analytics): units passing after rework.
* Run pass rate: passing runs over all runs, including retests.

### Measurement drift metrics

Custom measurement drift rules support these metrics:

* [Cpk drift](/docs/run-analytics/measurement-control#process-capability-index-cpk): percentage drop in Cpk against the baseline. Requires spec limits.
* Mean drift: shift of the mean in process sigmas. Works without spec limits.
* Cpk floor: absolute Cpk minimum. Requires spec limits.

## Configure alerts

Use [Configure alerts](/docs/alerts/configure-alerts) to create alert rules, set custom thresholds, scope rules to procedures, stations, or parts, and choose email notification levels.

## Investigate alerts with AI

When you get a yield drop or measurement drift alert, [Root Cause Analysis](/docs/ai/root-cause-analysis) can help you debug. Instead of digging through runs and measurements yourself, it analyzes the alert's window and keeps its verdict, hypothesis tree, and next steps on the alert.

When you view an alert in the dashboard, click **Root Cause Analysis** to start an investigation. You can also click **Re-run Root Cause Analysis** to start a new investigation as new data lands.

Learn more in the [Root Cause Analysis docs](/docs/ai/root-cause-analysis).

## How alerts trigger

Automatic detection grades each metric against the line's own history and refuses to grade thin data. These are the minimums and thresholds behind each alert type:

| Setting                     | Value                                                                   |
| --------------------------- | ----------------------------------------------------------------------- |
| Yield drop severity         | Info ≥ 2σ, warning ≥ 3σ, critical ≥ 5σ                                  |
| Measurement drift severity  | Info ≥ 3σ, warning ≥ 4σ, critical ≥ 5σ                                  |
| Resolution threshold        | Below 1.5σ                                                              |
| Yield drop warm-up          | About 275 first-runs over the past 90 days                              |
| Measurement drift warm-up   | 70 values and 5 distinct values per series; full severity range at 100  |
| Custom yield minimum data   | 10 runs in the window, 100 in the baseline                              |
| Custom drift minimum data   | 10 recent and 30 baseline samples; Cpk metrics need 30 on both sides    |
| Comparison window           | Default 1 week, range 1 to 90 days; baseline is the preceding 3x window |
| Consecutive-run thresholds  | Maximum 200                                                             |
| Retest alerts per procedure | Maximum 3 per day, then aggregated into one warning                     |

Actual results depend on baseline history, variance, and the minimum-data checks. A raw drop alone does not guarantee an alert.

## How alerts resolve

An alert is an episode: one degradation produces one alert that stays open, escalates in place if severity rises, and closes when the line recovers.

| Alert type                                  | Resolves                                          |
| ------------------------------------------- | ------------------------------------------------- |
| Yield drop, measurement drift               | Automatically, once the metric recovers           |
| Golden sample failed, failing sample passed | Automatically, on the next passing or failing run |
| Unit retest threshold, run failed           | Manually only                                     |

Resolving an alert by hand snoozes the same condition for the rule's window, or for 1 hour under automatic detection. Resolving a retest alert also resets that unit's counter on the procedure.
