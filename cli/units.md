<!-- Source: https://www.tofupilot.com/docs/cli/units -->
# tofupilot units
URL: /docs/cli/units

Create, list, update, and delete TofuPilot units from the terminal, including serial number filtering and bulk operations.

***

title: tofupilot units
description: "Create, list, update, and delete TofuPilot units from the terminal, including serial number filtering and bulk operations."
sidebarTitleCode: tofupilot units
---------------------------------

The `units` command manages [Units](/docs/fundamentals/test-data-model#unit), which are the units under test.

## `ls`

The `ls` subcommand lists and filters units. The full filter list is: `--search-query`, `--ids`, `--serial-numbers`, `--part-numbers`, `--revision-numbers`, `--batch-numbers`, `--procedure-ids`, `--outcomes`, `--started-after`, `--started-before`, `--latest-only`, `--run-count-min`, `--run-count-max`, `--created-after`, `--created-before`, `--created-by-user-ids`, `--created-by-station-ids`, `--exclude-units-with-parent`, `--samples`, `--limit`, `--cursor`, `--sort-by`, `--sort-order`, `--metadata`, and `--include-metadata`.

```bash title="list units"
tofupilot units ls --part-numbers PCB01 --latest-only true
```

## `get`

The `get` subcommand fetches one unit by serial number.

```bash title="get unit"
tofupilot units get --serial-number SN-0001
```

## `create`

The `create` subcommand creates a unit. Required flags are `--serial-number`, `--part-number`, and `--revision-number`. Optional flags are `--sample` (`golden` or `failing`) and `--metadata` (JSON).

```bash title="create unit"
tofupilot units create --serial-number SN-0001 --part-number PCB01 --revision-number A
```

## `update`

The `update` subcommand updates a unit by serial number, and optional flags are `--new-serial-number`, `--part-number`, `--revision-number`, `--batch-number`, `--attachments`, `--sample`, and `--metadata`.

```bash title="update unit"
tofupilot units update --serial-number SN-0001 --batch-number BATCH-2026-05
```

## `add-child`

The `add-child` subcommand links a sub-unit under a parent unit.

```bash title="add sub-unit"
tofupilot units add-child --serial-number SN-PARENT --child-serial-number SN-CHILD
```

## `remove-child`

The `remove-child` subcommand unlinks a sub-unit from its parent.

```bash title="remove sub-unit"
tofupilot units remove-child --serial-number SN-PARENT --child-serial-number SN-CHILD
```

## `create-attachment`

The `create-attachment` subcommand initializes a file attachment on a unit.

```bash title="attach to unit"
tofupilot units create-attachment --serial-number SN-0001 --name calibration.pdf
```

## `delete-attachment`

The `delete-attachment` subcommand deletes one or more attachments from a unit.

```bash title="delete attachment"
tofupilot units delete-attachment --serial-number SN-0001 --ids att_1 att_2
```

## `rm`

The `rm` subcommand deletes one or more units by serial number.

```bash title="delete units"
tofupilot units rm --serial-numbers SN-0001 SN-0002
```
