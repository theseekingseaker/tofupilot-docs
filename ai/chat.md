<!-- Source: https://www.tofupilot.com/docs/ai/chat -->
# TofuPilot Chat
URL: /docs/ai/chat

Query runs, units, parts, yield, throughput, and measurement distributions with TofuPilot Chat, an AI assistant grounded in your organization's test data.

***

title: TofuPilot Chat
sidebarTitle: Chat
description: "Query runs, units, parts, yield, throughput, and measurement distributions with TofuPilot Chat, an AI assistant grounded in your organization's test data."
preview: true
-------------

TofuPilot Chat answers questions across every run, unit, part, batch, and station in your organization. It calls typed tools backed by the REST API, so every answer is grounded in real data with links you can click through to the dashboard.

## What you can ask

Chat is good at:

* **Counts and breakdowns.** "How many runs failed yesterday?" "Show me yield by station for the last 7 days."
* **Drill-downs.** "Which phase fails most on the FVT procedure?" "Top 10 measurements out of spec this week."
* **Comparisons.** "Compare yield between revision B and revision C." "Which station has the highest throughput?"
* **Cpk and SPC.** "What is the Cpk of `voltage_5v` over the last 1000 runs?" "Show me the distribution of `current_idle` for the last batch."
* **Phase pareto.** "Which units failed `power_on` twice in a row?" "Show me the failure pareto for the last batch."

## Available tools

The chat backend exposes typed tools the model can call. Every tool runs through the REST API with the calling user's permissions.

| Tool                         | Purpose                                                         |
| ---------------------------- | --------------------------------------------------------------- |
| `listProcedures`             | List procedures with filters.                                   |
| `listRuns`                   | List runs with outcome, date, procedure, station, unit filters. |
| `listUnits`                  | List units with serial, part, batch, revision filters.          |
| `listParts`                  | List parts.                                                     |
| `listStations`               | List stations.                                                  |
| `listBatches`                | List batches.                                                   |
| `getYield`                   | First-pass and final yield for a slice.                         |
| `getThroughput`              | Run volume per hour, day, or week.                              |
| `getFailures`                | Failure counts grouped by phase or measurement.                 |
| `getCpk`                     | Process capability for a numeric measurement.                   |
| `getYieldOverTime`           | Yield trend with configurable bucket.                           |
| `getFailurePareto`           | Pareto chart of failure reasons.                                |
| `getFailureRateByDimension`  | Failure rate broken down by station, revision, or batch.        |
| `getFailureRate2D`           | Two-dimensional failure-rate matrix.                            |
| `getMeasurementDistribution` | Histogram of a measurement value across runs.                   |
| `getFailureCorrelation`      | Correlation between two failure dimensions.                     |
| `listContinuousVariables`    | List numeric measurements available for analysis.               |
| `generateTableUrl`           | Build a dashboard link with the filters from the chat answer.   |

## Scope and permissions

Chat enforces the same access controls as the dashboard:

* Tools call the REST API with your session, so you only see data your role and [access group scope](/docs/access/groups) allow.
* Stations, viewers, and operators all use chat with their own scope.
* Tools that list resources respect filters set by [SSO group mapping](/docs/access/sso) and access group membership.

## Saving and sharing

Chat sessions persist per user. You can:

* Pick up a previous conversation from the sidebar.
* Save a tool result as a new insight on a [Report](/docs/run-analytics/reports) (chat results embed directly).
* Copy a chat message or a tool result to share with a teammate.

## Limits

A few hard limits apply to chat sessions:

* Each session is bounded by a token budget that compacts old turns automatically.
* Tool calls run sequentially within a step; the model can issue multiple steps per turn.
* File uploads to chat are not supported. Attach files to runs through the [REST API](/docs/api/rest-api) or the [CLI](/docs/cli/attachments).

## In the dashboard

Open the chat from the sidebar or press the keyboard shortcut. Each conversation has its own URL so you can link to it in tickets or Slack threads.
