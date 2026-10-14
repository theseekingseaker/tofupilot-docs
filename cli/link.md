<!-- Source: https://www.tofupilot.com/docs/cli/link -->
# tofupilot link
URL: /docs/cli/link

Bind a local procedure directory to a remote TofuPilot procedure so local runs can upload to the dashboard without pulling a deployment.

***

title: tofupilot link
description: "Bind a local procedure directory to a remote TofuPilot procedure so local runs can upload to the dashboard without pulling a deployment."
sidebarTitleCode: tofupilot link
--------------------------------

The `link` command binds a local procedure directory to a remote procedure on the dashboard. It writes a `tofupilot.json` file at the directory root, and a later `tofupilot run <path> --upload` syncs the run to the linked procedure. Run it once per directory; without an argument it links the current directory.

```bash title="link"
tofupilot link
```

The `tofupilot.json` it writes holds the linked procedure id and name. Commit it to share the link with your team, or ignore it for a per-developer binding.

## `<PATH>`

A positional argument pointing to the procedure directory to link. Defaults to the current directory.

```bash title="link a directory"
tofupilot link ./procedures/pcb-fvt
```

## `--procedure <ID|NAME>`

To skip the interactive picker, pass `--procedure` with a procedure id or name. This is the form to use in non-interactive environments such as CI.

```bash title="link by name"
tofupilot link ./procedures/pcb-fvt --procedure "PCB Flash Test"
```

## `--json`

With `--json`, a successful link prints an object typed `"linked"` with `path`, `procedure_id`, and `procedure_name`. The matching `tofupilot unlink --json` prints `"unlinked"` (with `path` and `procedure_name`) or `"not_linked"` (with `path`) when the directory had no link.

```bash title="link json"
tofupilot link ./procedures/pcb-fvt --procedure 8122a38c-3fbc-4bf0-881b-24ea1e2cb937 --json
```

<Callout type="info">
  To run a linked procedure and upload the result, pass [`--upload`](/docs/cli/run) to `run`. The `TOFUPILOT_PROCEDURE_ID` environment variable overrides `tofupilot.json` when set.
</Callout>
