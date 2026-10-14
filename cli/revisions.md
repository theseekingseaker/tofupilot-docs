<!-- Source: https://www.tofupilot.com/docs/cli/revisions -->
# tofupilot revisions
URL: /docs/cli/revisions

Manage TofuPilot part revisions from the terminal so engineering change orders flow into your test data without touching the dashboard.

***

title: tofupilot revisions
description: "Manage TofuPilot part revisions from the terminal so engineering change orders flow into your test data without touching the dashboard."
sidebarTitleCode: tofupilot revisions
-------------------------------------

The `revisions` command manages part revisions.

## `get`

The `get` subcommand fetches a revision, and the required flags are `--part-number` and `--revision-number`.

```bash title="get revision"
tofupilot revisions get --part-number PCB01 --revision-number A
```

## `create`

The `create` subcommand creates a revision, and the required flags are `--part-number` and `--number`.

```bash title="create revision"
tofupilot revisions create --part-number PCB01 --number B
```

## `update`

The `update` subcommand updates a revision. Required flags are `--part-number` and `--revision-number`, and optional flags are `--number` (rename) and `--image-id`.

```bash title="rename revision"
tofupilot revisions update --part-number PCB01 --revision-number A --number A1
```

## `rm`

The `rm` subcommand deletes a revision, and the required flags are `--part-number` and `--revision-number`.

```bash title="delete revision"
tofupilot revisions rm --part-number PCB01 --revision-number A
```
