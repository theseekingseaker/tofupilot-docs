<!-- Source: https://www.tofupilot.com/docs/station/offline-upload -->
# Offline Upload Queue
URL: /docs/station/offline-upload

Learn how the TofuPilot CLI buffers runs locally when the dashboard is unreachable so no run is lost to a transient outage on the factory floor.

***

title: Offline Upload Queue
description: "Learn how the TofuPilot CLI buffers runs locally when the dashboard is unreachable so no run is lost to a transient outage on the factory floor."
---------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Callout } from 'fumadocs-ui/components/callout';

The offline upload queue is the local buffer the CLI uses to keep runs flowing when the network or the dashboard is unreachable. Every completed run is enqueued first and then uploaded, so a [Station](/docs/station) or [CLI](/docs/cli) session never loses a run to a transient outage.

## How it works

Every run produced by `tofupilot run`, the [OpenHTF](/docs/frameworks) connector, or CLI is persisted to the local queue **before** upload. A background drain loop wakes every 5 seconds, picks up due entries, and calls `runs.create` plus any attachment uploads, and because the original execution timestamp is part of the queued payload, analytics stay accurate no matter how late the upload lands.

| Behavior                           | Where                                               |
| ---------------------------------- | --------------------------------------------------- |
| Enqueue on run completion          | Every run path: YAML engine, OpenHTF, Pytest, Robot |
| Background drain (5s tick)         | Station daemon and `tofupilot studio`               |
| Retry with backoff (15s to 1h cap) | Transient failures: 5xx, 429, network, I/O          |
| Park (no auto-retry)               | Deterministic failures: 4xx, malformed JSON         |
| Manual unpark                      | `tofupilot queue retry`                             |

## Where the queue lives

The queue lives in an [redb](https://www.redb.org) database at `~/.tofupilot/state.redb`, and attachments are written to a sibling directory that the queue row references by path. The CLI holds an exclusive file lock on the database, so two CLI processes cannot corrupt the queue.

Removing the directory wipes every queued run that has not uploaded, so be careful with cleanup. `tofupilot uninstall --keep-data` preserves it, and `tofupilot uninstall` deletes it.

## Operator commands

When you need to inspect or steer the queue from the CLI, five commands cover the lifecycle. See the [queue command reference](/docs/cli/queue) for full detail.

```bash title="list-queue"
tofupilot queue
```

```bash title="queue-get"
tofupilot queue get <queue-id>
```

The `get` command shows one entry in full: status, attempts, retry schedule, last error, and attachment paths.

```bash title="queue-retry"
tofupilot queue retry
tofupilot queue retry <queue-id>
```

The `retry` command un-parks entries (including 4xx-parked rows) and drains immediately, so use it after fixing a payload or credential issue that parked the entry. With a queue ID it retries that single entry. The all-entries form exits non-zero if any entry is still queued after the pass; the single-entry form exits non-zero if that entry's upload fails again.

```bash title="queue-rm"
tofupilot queue rm <queue-id>
tofupilot queue rm --all
```

The `rm` command hard-deletes the entry and its on-disk attachments, and there is no recovery, so only remove entries you have inspected. To keep a copy of the run data first, `tofupilot queue export <queue-id> --out run.json` writes the create-run payload to disk — note this saves the payload only, not the attachment files, so copy those from the paths shown by `queue get` if you need them.

## Claims and duplicate prevention

The drain loop and manual retries can race on the same entry, and the CLI guards against this with a process-wide claim set. An upload acquires the claim before calling `runs.create` and releases it on success, failure, or panic, so the loser no-ops silently and the same queued run never mints two dashboard rows.

Once `runs.create` succeeds, the server-issued `run_id` is persisted on the queue row, so a later retry (for example, when an attachment failed) skips the create call and resumes from the attachment step.

That covers a failure the CLI learned about. It does not cover a lost response: the server created the run and answered, but the answer never reached the station (a proxy dropped it, or the request timed out client-side). The queue then sees a failed attempt and retries. Since CLI 1.5.0, every queued run carries an idempotency reference (`client_run_ref`), minted and persisted before the first attempt is sent. The retry sends the same reference and the server returns the run it already created instead of storing a second one. Earlier CLI versions send no reference, and a lost response can duplicate the run.

## Station vs CLI behavior

The drain loop runs in both modes, but each surfaces queue progress differently.

**Station mode** runs the drain loop for the daemon's lifetime, and live progress events emit over the local WebSocket: `RunUploadQueued`, `RunUploadStarted`, `RunUploadSucceeded`, and `RunUploadFailed`. The [Operator UI](/docs/frameworks/tofupilot/operator-ui) renders these in the Uploads panel.

**CLI mode** runs the same loop and also exposes the queue through `/api/v1/queue/list`, so the CLI reports page can correlate each queue row with the on-disk `report.json` that produced it. Double-clicking Upload on the same report returns the existing `queue_id`, so there is no duplicate enqueue.

<Callout type="warn">
  A station that stays offline for days grows the queue without bound because the CLI never drops entries on its own. Monitor `tofupilot queue` and prune with `rm` if a station has been disconnected long enough to fill disk.
</Callout>
