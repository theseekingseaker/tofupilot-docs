<!-- Source: https://www.tofupilot.com/docs/cli/attachments -->
# tofupilot attachments
URL: /docs/cli/attachments

Drive the TofuPilot two-step upload directly from the terminal to script raw multipart uploads of reports, logs, and images outside the run flow.

***

title: tofupilot attachments
description: "Drive the TofuPilot two-step upload directly from the terminal to script raw multipart uploads of reports, logs, and images outside the run flow."
sidebarTitleCode: tofupilot attachments
---------------------------------------

The `attachments` command exposes the two-step upload primitives used internally by `runs create-attachment` and `units create-attachment`. Use these directly when you script raw multipart uploads.

## `initialize`

The `initialize` subcommand initializes an upload. The required flag is `--name`. Pass `--json` to capture the upload ID and presigned URL in the output; human mode prints a success line without them.

```bash title="init upload"
tofupilot attachments initialize --name report.pdf --json
```

## `finalize`

The `finalize` subcommand finalizes an upload after the bytes have been pushed to the presigned URL, and the required flag is `--id`.

```bash title="finalize upload"
tofupilot attachments finalize --id upload_abc
```
