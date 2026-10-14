<!-- Source: https://www.tofupilot.com/docs/integrations/workflows/versions -->
# Versions
URL: /docs/integrations/workflows/versions

Learn how TofuPilot snapshots every published workflow change as an immutable version so executions replay the exact logic that was live.

***

title: Versions
description: "Learn how TofuPilot snapshots every published workflow change as an immutable version so executions replay the exact logic that was live."
--------------------------------------------------------------------------------------------------------------------------------------------------------

import { Callout } from "fumadocs-ui/components/callout";

A version is an immutable snapshot of a workflow's flow definition: its nodes, edges, and configuration. Executions replay against the exact logic that was live when they fired, so you can roll back a bad edit by restoring a prior version.

## When versions are created

TofuPilot snapshots a workflow on the two events that move flow into production.

* **On activation**: moving from `draft` or `paused` to `active` snapshots the current flow.
* **On live edit**: saving a change while the workflow is `active` snapshots the new flow.

A content hash over the nodes and edges deduplicates the snapshot, so saving without changing execution-relevant content does not create a new version. Editor-only details such as note positions do not bump the version.

You can also force a snapshot from the version dropdown, and forced snapshots skip the dedupe check.

<Callout>
  Drafts do not auto-version. Until first activation, edits overwrite the working copy. First activation creates version 1.
</Callout>

## Browsing history

The header version badge opens the history dropdown, and each entry summarizes the snapshot at a glance.

* Version number (monotonic, starts at 1).
* Created-at timestamp.
* The [member](/docs/access/managing-members) who triggered the snapshot.
* Whether it is the currently active version.

Selecting a past version loads its flow into a read-only canvas.

## Diffing

There is no first-party visual diff today. You can compare two versions by opening them in two browser tabs side by side.

## Restoring

Restoring points the workflow at a prior version, and the status is preserved. If the workflow was `active`, the restored flow takes effect immediately for incoming triggers.

Restore does not create a new version. It rewinds. The next edit and re-activation creates the next version number.

## What each version records

Every version row carries a fixed set of fields that together pin down what ran.

| Field           | Notes                                        |
| --------------- | -------------------------------------------- |
| Version id      | Unique identifier.                           |
| Workflow        | The workflow this version belongs to.        |
| Version number  | Monotonic, starts at 1, unique per workflow. |
| Flow definition | Full nodes + edges snapshot.                 |
| Created by      | Member who triggered the snapshot.           |
| Created at      | Snapshot timestamp.                          |

Past executions remember which version they ran against, so the canvas overlay renders against the flow that ran, even after later edits or restores.

## Permissions

Anyone with `update` on `Workflow` can create, restore, and list versions. Read-only members see history but cannot restore. See [access roles](/docs/access/access-roles#permissions) for the full grant matrix.
