<!-- Source: https://www.tofupilot.com/docs/cli/procedures -->
# tofupilot procedures
URL: /docs/cli/procedures

List, inspect, and manage TofuPilot procedures from the terminal to bind code repositories to test stations on the factory floor.

***

title: tofupilot procedures
description: "List, inspect, and manage TofuPilot procedures from the terminal to bind code repositories to test stations on the factory floor."
sidebarTitleCode: tofupilot procedures
--------------------------------------

The `procedures` command manages [Procedures](/docs/fundamentals/test-data-model#procedure).

## `ls`

The `ls` subcommand lists procedures. Filters are `--limit`, `--cursor`, `--search-query`, `--created-after`, and `--created-before`. The `ID` column shows the full procedure ID expected by `get`, `update`, and `rm`.

```bash title="list procedures"
tofupilot procedures ls --search-query "FVT"
```

## `get`

The `get` subcommand fetches a procedure by ID.

```bash title="get procedure"
tofupilot procedures get --id 8122a38c-3fbc-4bf0-881b-24ea1e2cb937
```

## `create`

The `create` subcommand creates a procedure, and the only required flag is `--name`.

```bash title="create procedure"
tofupilot procedures create --name "PCB Final Validation"
```

## `update`

The `update` subcommand updates a procedure by ID. Optional flags are `--name`, `--production-branch`, `--auto-push-enabled`, `--excluded-branch-patterns`, `--root-directory`, and `--entry-point`.

```bash title="set production branch"
tofupilot procedures update --id 8122a38c-3fbc-4bf0-881b-24ea1e2cb937 --production-branch main
```

### `--entry-point`

The `--entry-point` flag sets which file the station runs, as a path relative to the procedure's package directory. It is the setting that lets several procedures share one codebase: each procedure points at its own file instead of renaming everything to `main.py`. The same setting is available on the procedure's settings page in the dashboard.

```bash title="set entry point"
tofupilot procedures update --id 8122a38c-3fbc-4bf0-881b-24ea1e2cb937 --entry-point procedure1.py
```

When the entry point is empty, each framework falls back to its default:

| Framework                | Default when unset                                                           |
| ------------------------ | ---------------------------------------------------------------------------- |
| OpenHTF / plain Python   | `main.py` in the package directory. That exact name, no other file is tried. |
| pytest / Robot Framework | The whole package directory (test discovery).                                |
| YAML                     | `procedure.yaml` (or `.yml`) auto-discovery.                                 |

An entry point ending in `.yaml`/`.yml` forces the procedure to run as a YAML procedure regardless of the file's name. This is the only way to run a procedure file not named `procedure.yaml`. For pytest and Robot Framework, the entry point can also name a subdirectory to narrow discovery to it.

**Accepted values.** A relative path inside the package directory, up to 256 characters, with segments made of letters, digits, `_`, `.`, and `-`. A single `.` means "the package directory itself" (useful for pytest). Everything else is rejected:

| Valid              | Invalid                                              |
| ------------------ | ---------------------------------------------------- |
| `main.py`          | `/abs/path.py` (absolute)                            |
| `procedure1.py`    | `../outside.py` (traversal)                          |
| `tests/smoke.py`   | `./main.py` (leading `./`)                           |
| `battery-eol.yaml` | `main.py --flag` (no arguments, spaces are rejected) |
| `.`                | `entry file.py` (spaces)                             |

**When it takes effect.** The entry point is baked into the deployment manifest at build time, not read live: changing it only affects the next deployment. Stations need CLI 0.21.17 or newer; older stations silently fall back to `main.py`.

**Imports from a nested entry point.** The station runs the procedure with the package directory as working directory and import root, not the entry file's own directory. An entry point at `procedures/procedure1.py` therefore imports a sibling `procedures/helpers.py` as `from procedures import helpers`, not `import helpers`.

For local testing, [`tofupilot run`](/docs/cli/run) accepts the file directly (`tofupilot run ./procedure1.py`) without touching the procedure setting. See [multiple procedures, one codebase](/docs/frameworks/openhtf#multiple-procedures-one-codebase) for the full workflow.

### `--root-directory`

The `--root-directory` flag sets the path within the linked repository to the directory holding this procedure's `pyproject.toml` (and `procedure.yaml` for YAML procedures). Empty means the repository root. This is how a monorepo maps each procedure to its own subdirectory for [Git deployments](/docs/deployments/git); see [Monorepos](/docs/frameworks/tofupilot/monorepos).

```bash title="set root directory"
tofupilot procedures update --id 8122a38c-3fbc-4bf0-881b-24ea1e2cb937 --root-directory procedures/laptop-battery-functional
```

Accepted values follow the same rules as `--entry-point` (relative path, same character set), except `.` and `..` segments are always rejected. The `--entry-point` path is resolved relative to this directory.

## `rm`

The `rm` subcommand deletes a procedure by ID.

```bash title="delete procedure"
tofupilot procedures rm --id 8122a38c-3fbc-4bf0-881b-24ea1e2cb937
```
