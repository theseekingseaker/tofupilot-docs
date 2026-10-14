<!-- Source: https://www.tofupilot.com/docs/cli/unlink -->
# tofupilot unlink
URL: /docs/cli/unlink

Remove the link between a local procedure directory and its remote TofuPilot procedure by deleting tofupilot.json. Idempotent and safe to run twice.

***

title: tofupilot unlink
description: "Remove the link between a local procedure directory and its remote TofuPilot procedure by deleting tofupilot.json. Idempotent and safe to run twice."
sidebarTitleCode: tofupilot unlink
----------------------------------

The `unlink` command removes the link from a local procedure directory by deleting its `tofupilot.json`. Without an argument it unlinks the current directory. It is idempotent, so unlinking a directory that was never linked is a no-op.

```bash title="unlink"
tofupilot unlink
```

## `<PATH>`

A positional argument pointing to the procedure directory to unlink. Defaults to the current directory.

```bash title="unlink a directory"
tofupilot unlink ./procedures/pcb-fvt
```
