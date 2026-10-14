<!-- Source: https://www.tofupilot.com/docs/integrations/workflows/lifecycle -->
# Lifecycle
URL: /docs/integrations/workflows/lifecycle

Learn how to archive, unarchive, duplicate, and delete TofuPilot workflows, and how each status governs whether triggers fire.

***

title: Lifecycle
description: "Learn how to archive, unarchive, duplicate, and delete TofuPilot workflows, and how each status governs whether triggers fire."
---------------------------------------------------------------------------------------------------------------------------------------------

import { Callout } from "fumadocs-ui/components/callout";

A TofuPilot workflow moves through four statuses: `draft`, `active`, `paused`, and `archived`. Only `active` workflows respond to triggers.

## Archive

Archive is a soft delete. The workflow disappears from the main list, its status flips to `archived`, and `deletedAt` is recorded on the row.

* Firing stops. Event triggers are skipped and schedule triggers have `nextRunAt` cleared.
* Existing [executions](/docs/integrations/workflows/executions) and [versions](/docs/integrations/workflows/versions) are kept.
* The workflow can be unarchived later.

## Unarchive

Restoring an archived workflow clears `deletedAt` and sets status to `paused`. The workflow never goes directly to `active`, so you re-activate it from the editor when you are ready to start firing.

The original schedule, flow, and version history are intact when you unarchive.

## Duplicate

Duplicate creates a new workflow row with the same flow definition and description, named `Copy of <original>`. The copy starts fresh on a few key dimensions.

* It starts in `draft`.
* It has no version history. Versioning starts fresh on first activation.
* It has no executions.
* It is owned by the user who clicked **Duplicate**.

Names longer than the limit are truncated to fit the `Copy of ` prefix.

## Delete

Delete is a hard delete. It removes the workflow row and cascades to every related record.

* Every version snapshot.
* Every execution.
* Every per-step log.

There is no recovery once delete is confirmed.

<Callout type="warn">
  Archived workflows cannot be deleted directly. Unarchive first.
</Callout>

`delete` on `Workflow` is required separately from `update` (which covers archive and unarchive). See [access roles](/docs/access/access-roles#permissions) for the full grant matrix.

## Status transitions

The status machine is small, and only a handful of transitions are valid.

| From                        | To         | Triggered by                                               |
| --------------------------- | ---------- | ---------------------------------------------------------- |
| `draft`                     | `active`   | Activate in editor. Validates trigger and connectivity.    |
| `active`                    | `paused`   | Pause in editor. Clears `nextRunAt`.                       |
| `paused`                    | `active`   | Re-activate. Recomputes `nextRunAt` for schedule triggers. |
| `active`, `paused`, `draft` | `archived` | Archive. Sets `deletedAt`.                                 |
| `archived`                  | `paused`   | Unarchive. Clears `deletedAt`.                             |

Activation requires a trigger, an incoming edge on every non-trigger node, and every `if/else` and `switch` branch wired.

## Behavior by status

The table below summarizes what each status allows so you can pick the right one for a given moment.

| Behavior                   | `active` | `paused` | `archived`           |
| -------------------------- | -------- | -------- | -------------------- |
| Responds to event triggers | yes      | no       | no                   |
| Fires on schedule          | yes      | no       | no                   |
| Can be triggered manually  | yes      | no       | no                   |
| Visible in main list       | yes      | yes      | no                   |
| Executions queryable       | yes      | yes      | yes                  |
| Versions queryable         | yes      | yes      | yes                  |
| Can be edited              | yes      | yes      | no                   |
| Can be deleted             | yes      | yes      | no (unarchive first) |

## Identity

The status enum lives on the `workflow` row, and `deletedAt` is the soft-delete flag. A workflow is "live" when `deletedAt IS NULL`, regardless of whether its status is `draft`, `active`, or `paused`. Archived workflows are filtered out of standard queries.
