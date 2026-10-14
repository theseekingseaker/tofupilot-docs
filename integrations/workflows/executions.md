<!-- Source: https://www.tofupilot.com/docs/integrations/workflows/executions -->
# Executions
URL: /docs/integrations/workflows/executions

Learn how to inspect every TofuPilot workflow run with per-node logs, trigger source, duration, and status from the Executions tab.

***

title: Executions
description: "Learn how to inspect every TofuPilot workflow run with per-node logs, trigger source, duration, and status from the Executions tab."
--------------------------------------------------------------------------------------------------------------------------------------------------

import { Callout } from "fumadocs-ui/components/callout";

An execution is a single end-to-end run of a workflow. Every time a trigger fires, TofuPilot produces one execution row that is snapshotted against the workflow version active at that moment.

Open a workflow and switch to **Executions** to browse the history. The flow diagram replays per-node status on the canvas, so you can see exactly which path the execution took.

## Status

Each execution moves through a small set of statuses that tell you whether the run made it through.

| Status      | Meaning                                                                  |
| ----------- | ------------------------------------------------------------------------ |
| `pending`   | Trigger fired. Queued for the worker.                                    |
| `running`   | Worker is stepping through nodes.                                        |
| `completed` | All nodes finished successfully (including `skipped` branches).          |
| `failed`    | A node threw and stopped the run. The `error` field carries the message. |

<Callout>
  Hard cap: 5 minutes per execution. Anything past the cap is marked `failed`.
</Callout>

There is no `canceled` status and no in-flight cancel button. To stop further executions, pause the workflow from the editor header.

## Trigger source

Every execution records the entity that produced it, so you can jump from the execution back to the run, unit, or schedule that fired it.

| Trigger            | Source                                    |
| ------------------ | ----------------------------------------- |
| `run_created`      | Run                                       |
| `unit_created`     | Unit                                      |
| `batch_created`    | Batch                                     |
| `part_created`     | Part                                      |
| `revision_created` | Part revision                             |
| `schedule`         | No source entity. Fires from `nextRunAt`. |
| `manual`           | The user who clicked **Run**.             |

For schedule triggers, the page header shows the next scheduled fire time while the workflow is active.

## Per-node logs

Each node emits a step log with its status (`pending`, `running`, `completed`, `failed`, `skipped`), duration, input variables, output variables, and error message. The list view aggregates step counts per execution, so you can scan failures at a glance.

Click any node in the canvas overlay to see the full input/output JSON for that step.

## Retries

There is no execution-level retry. A failed execution stays failed.

The `http_request` action supports up to 3 retries on transient errors, and these count as one execution. The step log records only the final attempt.

## Manual re-run

Re-firing an execution means firing the trigger again, and how you do that depends on the trigger type.

* **Manual triggers**: hit **Run** in the editor header. This only works while the workflow is `active`.
* **Event triggers**: there is no per-execution re-run. Re-create the source entity, or switch the workflow to a `manual` trigger for testing.

Re-runs execute against the workflow's current version, not the version that produced the original execution. For bit-for-bit reproduction, [restore the prior version](/docs/integrations/workflows/versions) first.

## Stats and filtering

The execution list returns rolling counts for `completed`, `failed`, `running`, and `pending`, plus the average duration over completed runs.

Pagination is cursor-based at 50 rows per page, newest first.

## What each execution records

Every execution carries a fixed set of fields that together let you audit the run end-to-end.

| Field               | Notes                                                                                   |
| ------------------- | --------------------------------------------------------------------------------------- |
| Execution id        | Unique identifier.                                                                      |
| Workflow            | The workflow this belongs to.                                                           |
| Version             | The workflow snapshot used. Older executions may be missing this.                       |
| Status              | `pending`, `running`, `completed`, `failed`.                                            |
| Trigger             | Which event fired this execution.                                                       |
| Trigger payload     | Raw event data.                                                                         |
| Source entity       | Link to the run, unit, batch, part, or revision. Empty if deleted.                      |
| Triggered by        | Member who clicked **Run** (manual only). See [members](/docs/access/managing-members). |
| Started / completed | Timestamps.                                                                             |
| Duration            | Total wall-clock time.                                                                  |
| Error               | Top-level error message if failed.                                                      |

Each step within an execution carries its own log entry keyed by execution id. Deleting a workflow removes its executions and step logs.

## Failures

Workflow execution failures do not surface as platform alerts, so you should wire the workflow itself to a notification action such as Slack, Discord, or email if you want to be told when something breaks.
