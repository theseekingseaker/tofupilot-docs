<!-- Source: https://www.tofupilot.com/docs/cli/batches -->
# tofupilot batches
URL: /docs/cli/batches

Manage TofuPilot production batches from the terminal to group units, track first-pass yield, and tie runs back to a manufacturing lot.

***

title: tofupilot batches
description: "Manage TofuPilot production batches from the terminal to group units, track first-pass yield, and tie runs back to a manufacturing lot."
sidebarTitleCode: tofupilot batches
-----------------------------------

The `batches` command manages production [Batches](/docs/fundamentals/test-data-model#batch).

## `ls`

The `ls` subcommand lists batches. Filters are `--ids`, `--numbers`, `--created-after`, `--created-before`, `--limit`, `--cursor`, `--search-query`, `--part-numbers`, `--revision-numbers`, `--sort-by`, and `--sort-order`.

```bash title="list batches"
tofupilot batches ls --part-numbers PCB01
```

## `get`

The `get` subcommand fetches a batch by number.

```bash title="get batch"
tofupilot batches get --number BATCH-2026-05
```

## `create`

The `create` subcommand creates a batch, and the only required flag is `--number`.

```bash title="create batch"
tofupilot batches create --number BATCH-2026-05
```

## `update`

The `update` subcommand renames a batch. Required flags are `--number` (current) and `--new-number`.

```bash title="rename batch"
tofupilot batches update --number BATCH-2026-05 --new-number BATCH-2026-05B
```

## `rm`

The `rm` subcommand deletes a batch by number.

```bash title="delete batch"
tofupilot batches rm --number BATCH-2026-05
```
