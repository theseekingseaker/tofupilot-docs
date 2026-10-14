<!-- Source: https://www.tofupilot.com/docs/cli/imports -->
# tofupilot imports
URL: /docs/cli/imports

Import TofuPilot runs from structured or tabular files via the terminal, using inline column mappings or saved templates.

***

title: tofupilot imports
description: "Import TofuPilot runs from structured or tabular files via the terminal, using inline column mappings or saved templates."
sidebarTitleCode: tofupilot imports
-----------------------------------

The `imports` command creates [Runs](/docs/fundamentals/test-data-model#run) from existing files, covering both structured report formats and tabular files like CSV. See [Import](/docs/import) for the supported formats.

## `structured`

The `structured` subcommand imports runs from structured files. The required flag is `--items` (JSON), a list of 1 to 100 files where each item is parsed independently, so one failure does not abort the others. Each item carries an `upload_id` (from the two-step [upload](/docs/cli/attachments)) and an `importer` naming the source format (`OPENHTF`, `WATS`, `WSXF`, `ATML`, `TESTSTAND`, `STDF`, or `ATDF`).

```bash title="import structured files"
tofupilot imports structured --items '[{"upload_id":"upload_abc","importer":"OPENHTF"}]'
```

## `tabular`

The `tabular` subcommand imports a run from a tabular file. Required flags are `--upload-id` (a previously uploaded file) and `--procedure-id` (always overrides any procedure referenced in the file). Pass either `--mapping` (inline JSON column mapping) or `--template-id` (a saved mapping template), not both.

```bash title="import tabular file"
tofupilot imports tabular --upload-id upload_abc --procedure-id proc_abc --template-id tpl_abc
```
