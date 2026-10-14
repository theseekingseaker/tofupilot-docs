<!-- Source: https://www.tofupilot.com/docs/cli/runs -->
# tofupilot runs
URL: /docs/cli/runs

List, inspect, and delete TofuPilot test runs from the terminal with JSON output for scripts and CI pipelines.

***

title: tofupilot runs
description: "List, inspect, and delete TofuPilot test runs from the terminal with JSON output for scripts and CI pipelines."
sidebarTitleCode: tofupilot runs
--------------------------------

The `runs` command manages test [Runs](/docs/fundamentals/test-data-model#run) via the [REST API](/docs/api/rest-api), and every subcommand accepts `--json`.

## `ls`

The `ls` subcommand lists and filters runs, and it supports filters by procedure, serial, outcome, time window, operator, station, batch, part, revision, and metadata. Pagination uses `--limit` and `--cursor`.

```bash title="filter runs"
tofupilot runs ls --procedure-ids proc_abc --outcomes PASS --limit 50
```

The full filter list is: `--search-query`, `--ids`, `--outcomes`, `--procedure-ids`, `--procedure-versions`, `--deployment-ids`, `--environments`, `--serial-numbers`, `--samples`, `--part-numbers`, `--revision-numbers`, `--batch-numbers`, `--duration-min`, `--duration-max`, `--started-after`, `--started-before`, `--ended-after`, `--ended-before`, `--created-after`, `--created-before`, `--created-by-user-ids`, `--created-by-station-ids`, `--operated-by-ids`, `--operated-by-names`, `--limit`, `--cursor`, `--sort-by`, `--sort-order`, `--metadata`, and `--include-metadata`.

## `get`

The `get` subcommand fetches a single run by ID.

```bash title="get run"
tofupilot runs get --id run_abc123
```

## `create`

The `create` subcommand creates a run. Required flags are `--outcome`, `--procedure-id`, `--started-at`, `--ended-at`, and `--serial-number`. Optional flags are `--deployment-id`, `--procedure-version`, `--operated-by`, `--part-number`, `--revision-number`, `--batch-number`, `--sub-units`, `--docstring`, `--phases` (JSON), `--logs` (JSON), `--metadata` (JSON), and `--unit-metadata` (JSON).

```bash title="create run"
tofupilot runs create \
  --outcome PASS \
  --procedure-id proc_abc \
  --serial-number SN-0001 \
  --started-at 2026-05-19T08:00:00Z \
  --ended-at 2026-05-19T08:00:42Z
```

## `update`

The `update` subcommand updates a run by ID, and it currently supports attaching uploads via `--attachments`.

```bash title="attach to run"
tofupilot runs update --id run_abc --attachments att_1 att_2
```

## `update-metadata`

The `update-metadata` subcommand PATCH-upserts custom metadata on a run, and you can pass `null` as a value to delete a key.

```bash title="update metadata"
tofupilot runs update-metadata --id run_abc --metadata '{"shift":"A","temp":23.4}'
```

## `create-attachment`

The `create-attachment` subcommand initializes a file attachment on a run. With `--json`, the output includes the attachment ID and a presigned upload URL that you pair with `tofupilot attachments finalize` after uploading bytes; in human mode the command prints a success line without the URL.

```bash title="attach file"
tofupilot runs create-attachment --id run_abc --name report.pdf
```

## `rm`

The `rm` subcommand deletes one or more runs by ID.

```bash title="delete runs"
tofupilot runs rm --ids run_a run_b
```
