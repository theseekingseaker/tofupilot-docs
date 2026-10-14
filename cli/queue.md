<!-- Source: https://www.tofupilot.com/docs/cli/queue -->
# tofupilot queue
URL: /docs/cli/queue

Inspect, retry, export, or remove offline TofuPilot runs queued on the station while connectivity was down, then upload them when the network returns.

***

title: tofupilot queue
description: "Inspect, retry, export, or remove offline TofuPilot runs queued on the station while connectivity was down, then upload them when the network returns."
sidebarTitleCode: tofupilot queue
---------------------------------

The `queue` command manages the offline upload queue. When a station runs offline, completed runs queue locally and upload when connectivity returns, and without a subcommand `queue` lists queued entries.

```bash title="list queue"
tofupilot queue
tofupilot queue ls
```

Add the global `--json` flag to any subcommand for machine-readable output.

## `get`

The `get` subcommand shows the full detail of a single queued entry: status, attempt count, retry schedule, last error, and attachments.

```bash title="queue get"
tofupilot queue get queue_xyz789
```

## `retry`

The `retry` subcommand force-retries every pending upload, or a single entry when given a queue ID. Both forms un-park entries that failed with a deterministic error, and both require authentication. The all-entries form exits non-zero if any entry is still queued after the pass; the single-entry form exits non-zero if that entry's upload fails again. With `--json`, the single-entry form reports the created run ID on success or the persisted failure record, and the all-entries form reports the remaining queue count.

```bash title="queue retry"
tofupilot queue retry
tofupilot queue retry queue_xyz789
```

## `rm`

The `rm` subcommand removes a single queued entry by ID, or every entry with `--all`. Removal hard-deletes the entry and its on-disk attachments with no recovery.

```bash title="queue rm"
tofupilot queue rm queue_xyz789
tofupilot queue rm --all
```

## `export`

The `export` subcommand prints the entry's create-run payload as JSON, or writes it to a file with `--out`. The payload is the exact request body the queue would send to the create-run API, useful for archiving or debugging before removing an entry. It does not include attachment files, only their queue metadata.

The payload carries the entry's idempotency reference (`client_run_ref`). Re-submitting an exported payload to the create-run API therefore returns the run already created, if there is one, instead of creating a duplicate. Remove the `client_run_ref` field from the payload if you want a new run from it.

```bash title="queue export"
tofupilot queue export queue_xyz789
tofupilot queue export queue_xyz789 --out run.json
```
