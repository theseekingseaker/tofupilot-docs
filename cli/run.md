<!-- Source: https://www.tofupilot.com/docs/cli/run -->
# tofupilot run
URL: /docs/cli/run

Execute a TofuPilot procedure locally from a YAML file, directory, or Python entry point, with the same operator UI and dashboard streaming as a factory station.

***

title: tofupilot run
description: "Execute a TofuPilot procedure locally from a YAML file, directory, or Python entry point, with the same operator UI and dashboard streaming as a factory station."
sidebarTitleCode: tofupilot run
-------------------------------

The `run` command executes a procedure. It takes an optional positional path to a procedure `.yaml` file, a directory, or a Python entry point, and when you pass a path, the procedure runs locally without contacting the dashboard. Without a path, `run` picks from pulled deployments.

```bash title="run a local procedure"
tofupilot run ./procedures/pcb-fvt
```

## `<PATH>`

A positional argument that points to a procedure `.yaml` file, a directory, or a Python entry point. This conflicts with `--deployment`.

```bash title="local path"
tofupilot run ./procedures/pcb-fvt
```

When the path is a directory, the entry point defaults to `main.py` (or the `procedure.yaml` inside it). When the path is a Python file, that file is the entry point, so a project with several procedures sharing one codebase can run any of them directly:

```bash title="explicit entry point"
tofupilot run ./procedure1.py
```

This is the local equivalent of the procedure's [`--entry-point` setting](/docs/cli/procedures#--entry-point), which deployed runs read from the deployment manifest.

## `--deployment <PROCEDURE_ID>`

To skip the interactive picker and run one pulled procedure directly, pass `--deployment` with the **procedure ID**. You cannot combine it with a path argument.

The procedure ID stays the same when you push a new version, so a station script that pins it keeps working across deployments. You can read it from the procedure page in the dashboard, or from the `ID` column of [`tofupilot procedures ls`](/docs/cli/procedures#ls).

```bash title="run by procedure id"
tofupilot run --deployment 550e8400-e29b-41d4-a716-446655440000
```

## `--ui-values <FILE>`

When you want to drive runs without an operator, pass a JSON file with pre-baked [Operator UI](/docs/frameworks/tofupilot/operator-ui) values keyed by `{ phase_key: { component_key: value } }`.

Pre-baked values are checked when the prompt opens, against the components declared in the procedure. A choice list the phase fills at run time with `ui.set` is empty at that moment, so its value cannot be pre-baked: answer that prompt with a `ui_response` after its `ui_update` arrives (see [JSON events](/docs/cli/json-events)).

```bash title="ui-values"
tofupilot run --ui-values phases.json
```

## `--ui-timeout <SECONDS>`

To time out phases waiting on required UI input, pass a number of seconds. The default is to wait forever.

```bash title="ui-timeout"
tofupilot run --ui-timeout 600
```

## `--tui`

The `--tui` flag force-enables the in-terminal operator UI for this run, overriding the `terminal_ui` station config. It conflicts with `--no-tui`.

```bash title="tui"
tofupilot run ./procedure.yaml --tui
```

## `--no-tui`

The `--no-tui` flag force-disables the in-terminal operator UI for this run, overriding the `terminal_ui` station config.

```bash title="no-tui"
tofupilot run ./procedure.yaml --no-tui
```

## `--kiosk`

The `--kiosk` flag force-enables the local browser kiosk UI for this run, and it spins up an in-process WebSocket and static-file server on loopback. It overrides the `kiosk_ui` station config and conflicts with `--no-kiosk`.

```bash title="kiosk"
tofupilot run --kiosk
```

## `--no-kiosk`

The `--no-kiosk` flag force-disables the local browser kiosk UI for this run, overriding the `kiosk_ui` station config.

```bash title="no-kiosk"
tofupilot run --no-kiosk
```

## `--no-bootstrap`

The `--no-bootstrap` flag skips the auto-bootstrap prompt for missing Python virtualenvs on local-path runs, so the run fails with the original Python resolution error instead of offering to provision a venv with `uv venv` and install dependencies.

```bash title="no-bootstrap"
tofupilot run ./procedure.yaml --no-bootstrap
```

## `--upload`

The `--upload` flag syncs a local-path run to the dashboard under the procedure it is [linked](/docs/cli/link) to. It requires a `tofupilot.json` in the procedure directory (written by `tofupilot link`) or the `TOFUPILOT_PROCEDURE_ID` environment variable, which takes precedence. Without `--upload`, a local-path run stays local and never contacts the dashboard. The flag is ignored for `--deployment` runs, which always upload.

```bash title="upload a linked local run"
tofupilot run ./procedures/pcb-fvt --upload
```

<Callout type="info">
  Studio, the desktop app, invokes the same engine as `tofupilot run`, so running from the terminal gives the same execution and dashboard sync as running from Studio.
</Callout>
