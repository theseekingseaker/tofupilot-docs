<!-- Source: https://www.tofupilot.com/docs/cli/parts -->
# tofupilot parts
URL: /docs/cli/parts

Create, list, update, and delete TofuPilot parts from the terminal to keep your part catalog in sync with engineering changes.

***

title: tofupilot parts
description: "Create, list, update, and delete TofuPilot parts from the terminal to keep your part catalog in sync with engineering changes."
sidebarTitleCode: tofupilot parts
---------------------------------

The `parts` command manages [Parts](/docs/fundamentals/test-data-model#part).

## `ls`

The `ls` subcommand lists parts. Filters are `--limit`, `--cursor`, `--search-query`, `--procedure-ids`, `--sort-by`, and `--sort-order`.

```bash title="list parts"
tofupilot parts ls
```

## `get`

The `get` subcommand fetches a part by number.

```bash title="get part"
tofupilot parts get --number PCB01
```

## `create`

The `create` subcommand creates a part. The required flag is `--number`, and optional flags are `--name` and `--revision-number`.

```bash title="create part"
tofupilot parts create --number PCB01 --name "Main board"
```

## `update`

The `update` subcommand updates a part by number, and optional flags are `--new-number` and `--name`.

```bash title="rename part"
tofupilot parts update --number PCB01 --name "Main control board"
```

## `rm`

The `rm` subcommand deletes a part by number.

```bash title="delete part"
tofupilot parts rm --number PCB01
```
