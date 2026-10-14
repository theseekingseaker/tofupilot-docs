<!-- Source: https://www.tofupilot.com/docs/frameworks/openhtf -->
# OpenHTF on TofuPilot
URL: /docs/frameworks/openhtf

Learn how to run existing OpenHTF suites through TofuPilot so phase outcomes, measurements, logs, and attachments upload to the dashboard automatically.

***

title: OpenHTF on TofuPilot
sidebarTitle: OpenHTF
description: "Learn how to run existing OpenHTF suites through TofuPilot so phase outcomes, measurements, logs, and attachments upload to the dashboard automatically."
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Callout } from 'fumadocs-ui/components/callout';

OpenHTF is a popular open-source Python hardware test framework maintained by Google, documented by TofuPilot at [openhtf.com](https://www.openhtf.com). It ships many hardware-test-specific features that general-purpose frameworks lack — [phases](https://www.openhtf.com/phases), [measurements with limits](https://www.openhtf.com/measurements), [plugs](https://www.openhtf.com/plugs) for instruments, an [operator UI](https://www.openhtf.com/operator-ui) and a structured [test record](https://www.openhtf.com/test-record) — making it an excellent choice for various test scenarios. New to it? Start with [What is OpenHTF?](https://www.openhtf.com/what-is-openhtf) and the [first test](https://www.openhtf.com/first-test).

With TofuPilot, you can deploy OpenHTF scripts to your stations via Git push and stream live test data and the operator UI to both the kiosk and the dashboard, with zero configuration.

## Getting started

To get started with OpenHTF on TofuPilot:

* Clone our OpenHTF starter template to your favorite Git provider and deploy it on TofuPilot.

<TemplateCards filter={['tofupilot/template-openhtf-starter']} />

* If you have an OpenHTF procedure, push it to your Git provider and import it from the **New Procedure** wizard.

## Integration

OpenHTF is a code-first Python test framework that uses decorators to attach measurements and shared resources to phase functions, then runs them sequentially against the unit under test.

```python title="main.py"
import openhtf as htf
from openhtf.util import units
from openhtf.plugs.user_input import UserInput

# Plug
class DMM(htf.plugs.BasePlug):
    def measure_voltage(self) -> float:
        return 5.03

# Phase
@htf.plug(dmm=DMM)  # inject plug
@htf.measures(htf.Measurement("voltage").in_range(4.8, 5.2).with_units(units.VOLT))  # validator
def measure_voltage(test, dmm):
    test.logger.info("Reading DMM")           # log
    test.measurements.voltage = dmm.measure_voltage()  # measurement
    test.attach_from_file("data/scope.png")   # attachment

# Operator prompt
@htf.plug(user_input=UserInput)
def prompt_serial(test, user_input):
    test.measurements.serial_number = user_input.prompt("Serial number?", text_input=True)  # operator input

htf.Test(prompt_serial, measure_voltage).execute()  # run phases on the unit under test
```

The TofuPilot CLI runs your OpenHTF scripts natively and streams phases, measurements, UI prompts, logs, and attachments to the dashboard.

### Phases

In OpenHTF, a phase is a Python function that receives a `test` context, runs one step of the procedure, and returns a `PhaseResult` to control whether the next phase runs.

```python title="phases.py"
import openhtf as htf

def power_on(test):
    return htf.PhaseResult.CONTINUE

htf.Test(power_on).execute()
```

TofuPilot maps OpenHTF phases to the `phase` resource as follows:

| OpenHTF                                                                                   | TofuPilot      | Notes                                                                                                   |
| ----------------------------------------------------------------------------------------- | -------------- | ------------------------------------------------------------------------------------------------------- |
| `name`                                                                                    | `name`         | Capped at 200 chars.                                                                                    |
| `outcome`                                                                                 | `outcome`      | Same enum: `PASS`, `FAIL`, `SKIP`, `ERROR`.                                                             |
| `start_time_millis`                                                                       | `startedAt`    | Epoch ms → ISO 8601 timestamp.                                                                          |
| `end_time_millis`                                                                         | `endedAt`      | Epoch ms → ISO 8601 timestamp.                                                                          |
| `codeinfo.docstring`                                                                      | `docstring`    | Capped at 50000 chars.                                                                                  |
| `result`                                                                                  | `retryCount`   | Only `PhaseResult.REPEAT` is tracked, as the retry count. Other control-flow values are not stored.     |
| `measurements`                                                                            | `measurements` | Internally linked to the phase.                                                                         |
| `attachments`                                                                             | `attachments`  | Flattened to the run level; phase association is not preserved.                                         |
| `marginal`                                                                                | not persisted  | Marginal validators on measurements are expanded into `>=` / `<=` validator entries on the measurement. |
| `options`, `subtest_name`, `diagnosers`, `diagnosis_results`, `failure_diagnosis_results` | not persisted  |                                                                                                         |

### Measurements

In OpenHTF, a measurement is a typed value declared with `@htf.measures` on a phase, optionally constrained by validators and tagged with units, then assigned at runtime via `test.measurements.<name>`.

```python title="measurement.py"
import openhtf as htf
from openhtf.util import units

@htf.measures(
    htf.Measurement("voltage").in_range(4.8, 5.2).with_units(units.VOLT)
)
def measure_voltage(test):
    test.measurements.voltage = 5.03
```

TofuPilot maps OpenHTF measurements to the `measurement` resource as follows:

| OpenHTF                                                   | TofuPilot                                       | Notes                                                                                                               |
| --------------------------------------------------------- | ----------------------------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| `name`                                                    | `name`                                          | Capped at 200 chars.                                                                                                |
| `outcome`                                                 | `outcome`                                       | OpenHTF `PASS` / `FAIL` map 1:1. `UNSET`, `PARTIALLY_SET`, `SKIPPED` are collapsed to `UNSET`.                      |
| `units`                                                   | `units`                                         | OpenHTF unit object reduced to its suffix string. Capped at 60 chars.                                               |
| `docstring`                                               | `docstring`                                     | Capped at 50000 chars.                                                                                              |
| `measured_value`                                          | `value`                                         | Routed to a typed table: `numeric_measurement`, `string_measurement`, `boolean_measurement`, or `json_measurement`. |
| `dimensions` + `measured_value`                           | `multi_dimensional_measurement` + `data_series` | Multi-dim measurements stored separately; per-axis validators not supported.                                        |
| `validators`                                              | expanded                                        | Each validator becomes a per-limit `>=` / `<=` entry on the measurement.                                            |
| `marginal`                                                | expanded                                        | Marginal validators are emitted as separate `>=` / `<=` entries flagged as marginal.                                |
| `set_time_millis`, `allow_fail`, `conditional_validators` | not persisted                                   |                                                                                                                     |

### Logs

In OpenHTF, every phase receives a `test.logger` — a standard Python logger that captures records into the test for later inspection.

```python title="log.py"
def measure_voltage(test):
    test.logger.info("Calibration started")
```

TofuPilot maps OpenHTF log records to the `log` resource as follows:

| OpenHTF            | TofuPilot     | Notes                                                       |
| ------------------ | ------------- | ----------------------------------------------------------- |
| `level`            | `level`       | Same enum: `DEBUG`, `INFO`, `WARNING`, `ERROR`, `CRITICAL`. |
| `timestamp_millis` | `timestamp`   | Epoch ms → ISO 8601 timestamp.                              |
| `message`          | `message`     | Capped at 50000 chars.                                      |
| `source`           | `sourceFile`  | Capped at 200 chars.                                        |
| `lineno`           | `lineNumber`  |                                                             |
| `logger_name`      | not persisted |                                                             |

Logs are stored at the run level; per-phase association is not preserved.

### Attachments

In OpenHTF, an attachment is binary content tied to a phase, declared via `test.attach(...)` or `test.attach_from_file(...)`.

```python title="attachment.py"
def capture(test):
    test.attach_from_file("data/scope.png")
```

TofuPilot maps OpenHTF attachments to the `upload` resource as follows:

| OpenHTF        | TofuPilot     | Notes                                                                |
| -------------- | ------------- | -------------------------------------------------------------------- |
| attachment key | `name`        | Capped at 200 chars.                                                 |
| `mimetype`     | `contentType` | Capped at 60 chars.                                                  |
| `size`         | `size`        | Bytes.                                                               |
| `sha1`         | `etag`        | Stored as the upload's S3 ETag; the OpenHTF SHA-1 itself is dropped. |

Attachments are linked to the run via `run_attachments`; per-phase association is not preserved.

### Operator UI

OpenHTF ships an `openhtf.plugs.user_input.UserInput` plug to ask the operator for input mid-run. TofuPilot replaces it with a CLI-side implementation that streams the prompt to the kiosk and the dashboard.

```python title="prompt.py"
import openhtf as htf
from openhtf.plugs.user_input import UserInput

@htf.plug(user_input=UserInput)
def prompt_serial(test, user_input):
    serial = user_input.prompt("Serial number?", text_input=True)
    test.measurements.serial_number = serial
```

TofuPilot supports the following `UserInput.prompt` arguments:

| OpenHTF      | TofuPilot | Notes                                                              |
| ------------ | --------- | ------------------------------------------------------------------ |
| `message`    | supported | Displayed verbatim on the kiosk and the dashboard.                 |
| `text_input` | supported | Renders a text input when `True`, otherwise an acknowledge button. |
| `timeout_s`  | supported | Auto-cancels the prompt after `timeout_s` seconds.                 |
| `image_url`  | supported | Rendered inline next to the prompt.                                |
| `cli_color`  | ignored   | TofuPilot has no CLI-color concept on the kiosk or dashboard.      |

### Plugs

In OpenHTF, a plug is a class subclassing `BasePlug`, declared with `@htf.plug(name=PlugClass)` on a phase. The framework instantiates the plug, injects it as a phase argument, and tears it down when the test ends.

```python title="plug.py"
import openhtf as htf

class DMM(htf.plugs.BasePlug):
    def measure_voltage(self) -> float:
        return 5.03

@htf.plug(dmm=DMM)
def measure(test, dmm):
    test.logger.info(f"voltage: {dmm.measure_voltage()}")
```

TofuPilot runs OpenHTF plugs as-is in Python. `setUp`, `tearDown`, and dependency injection work unchanged, but the dashboard does not surface plug instances, lifecycle events, or `FrontendAwareBasePlug` state. Only the data plugs emit (measurements, logs, attachments, prompts) is captured. `UserInput` is the one exception, replaced by a TofuPilot implementation that streams prompts.

### Run outcome

In OpenHTF, `test_record.outcome` summarizes the whole test (`PASS`, `FAIL`, `ERROR`, `TIMEOUT`, `ABORTED`).

TofuPilot maps the test outcome to the `run` resource as follows:

| OpenHTF                   | TofuPilot | Notes                                                                         |
| ------------------------- | --------- | ----------------------------------------------------------------------------- |
| `outcome` `PASS` / `FAIL` | `outcome` | 1:1 mapping.                                                                  |
| `outcome` `ERROR`         | `outcome` | Collapsed to `ERROR`; unhandled exceptions land here.                         |
| `outcome` `TIMEOUT`       | `outcome` | 1:1 mapping.                                                                  |
| `outcome` `ABORTED`       | `outcome` | 1:1 mapping. A stopped run lands here, see [Stopping a run](#stopping-a-run). |

### DUT ID

In OpenHTF, the DUT ID is resolved by the callable passed to `test.execute(...)` and stored on `test_record.dut_id`.

```python title="dut.py"
import openhtf as htf

def power_on(test):
    return htf.PhaseResult.CONTINUE

htf.Test(power_on).execute(lambda: "SN-0001")
```

TofuPilot uses the OpenHTF DUT ID as the unit serial number on the run. The `UserInput` plug can also drive it interactively when the operator types it into the kiosk prompt.

### Test metadata

In OpenHTF, `test_record.metadata` is a free-form dict mutated by phases (e.g. `test.metadata["serial_number"] = read_from_eeprom()`).

TofuPilot reads a known set of keys from `test_record.metadata` and promotes them to the run:

| OpenHTF metadata key | TofuPilot     | Notes                                                       |
| -------------------- | ------------- | ----------------------------------------------------------- |
| `procedure_id`       | `procedureId` | Procedure identifier on the run.                            |
| `part_number`        | `partNumber`  | Part number on the run.                                     |
| `revision`           | `revision`    | Part revision.                                              |
| `batch_number`       | `batchNumber` | Production batch.                                           |
| `sub_units`          | `subUnits`    | List of `{"serial_number": "..."}` for assembled units.     |
| `operated_by`        | `operatedBy`  | Operator who ran the test, as an email or a free-text name. |
| any other key        | not persisted | Stays on the Python `test.metadata` dict, not promoted.     |

### PhaseGroup

OpenHTF's `PhaseGroup` runs phases in three slots — `setup`, `main`, `teardown` — and guarantees teardown even if setup or main fails.

```python title="group.py"
import openhtf as htf

def power_on(test): ...
def measure(test): ...
def power_off(test): ...

htf.Test(htf.PhaseGroup(setup=[power_on], main=[measure], teardown=[power_off])).execute()
```

TofuPilot honors the OpenHTF runtime contract — phases execute in the right order and teardown still fires — but flattens the group at capture time. Phase rows appear in execution order with no `setup` / `main` / `teardown` tag; the grouping itself is not persisted.

### Stopping a run

A stop (the Stop button in the operator UI, Ctrl-C, SIGTERM) aborts the test the way OpenHTF does on its own Ctrl-C: the running phase is interrupted, the `PhaseGroup` teardown phases and the plugs' `tearDown` run, and the run is uploaded with outcome `ABORTED` and the phases that completed. A `UserInput` prompt open in the interrupted phase is withdrawn with it. A prompt in a teardown phase is not interrupted: OpenHTF leaves the teardown alone, so the prompt stays on screen for the operator to answer, within the 45 seconds below.

The teardown has 45 seconds. Past that, or on Kill (a second press of Stop), the run gets OpenHTF's forced abort, what its own second Ctrl-C does: the phase under way is stopped even if it is a teardown phase, the remaining teardown phases are skipped, the plugs' `tearDown` still runs, and the run is still uploaded as `ABORTED` with the phases that completed. Only a process that has not exited seven seconds after that is killed, and that run is not uploaded. OpenHTF spends up to two of those seconds waiting for the phase it killed, so the plugs' `tearDown` methods get about five seconds together. A plug `tearDown` that blocks is the usual cause of a kill: OpenHTF gives it no time limit unless `plug_teardown_timeout_s` is set. OpenHTF interrupts a phase between Python statements and gives it two seconds to exit (its `cancel_timeout_s` setting), then moves on to the teardown. A phase blocked in one long native call (a single `time.sleep(600)`, a blocking read with no timeout) keeps running in its thread until the process exits: poll in short steps in long phases so the stop is immediate and the thread does not outlive the test.

## Multiple procedures, one codebase

By default the CLI looks for a `main.py` entry point. When several procedures share one codebase, for example `procedure1.py`, `procedure2.py`, and `procedure3.py`, keep a single directory and create one TofuPilot procedure per file, each pointing at its own entry point. No renaming to `main.py` and no separate checkouts.

Set each procedure's entry point once (the procedure ID comes from [`tofupilot procedures ls`](/docs/cli/procedures) or the dashboard):

```bash title="one procedure per file"
tofupilot procedures update --id <procedure1-id> --entry-point procedure1.py
tofupilot procedures update --id <procedure2-id> --entry-point procedure2.py
tofupilot procedures update --id <procedure3-id> --entry-point procedure3.py
```

The entry point is baked into each deployment when it is built, so changing it takes effect on the next deploy. Stations need CLI 0.21.17 or newer; older versions silently fall back to `main.py`.

To test one of them locally, pass its file to [`tofupilot run`](/docs/cli/run) directly:

```bash title="run one procedure locally"
tofupilot run ./procedure1.py
```

To deploy, [link](/docs/cli/link) the directory to each procedure in turn and run [`tofupilot deploy`](/docs/cli/deploy), or link the repository once and let a push build every procedure with its own entry point, using [Git deployments](/docs/deployments/git).

## Running a subset of phases

To run only some phases of a test (a quick check on one bench, the full sequence on another), gate the optional phases with OpenHTF's `run_if` option instead of maintaining several copies of the test:

```python title="main.py"
import os
import openhtf as htf

RUN_BURNIN = os.environ.get("RUN_BURNIN", "0") == "1"

@htf.PhaseOptions(run_if=lambda: RUN_BURNIN)
def thermal_burnin(test):
    ...
```

See the [OpenHTF phases documentation](https://www.openhtf.com/phases) for the other `PhaseOptions`. Two consequences on the TofuPilot side:

* A phase skipped by `run_if` produces **no phase row on the run page**: it looks as if the phase were not part of the test. Since the `PhaseOptions` object itself is not persisted (see [Unsupported features](#unsupported-features)), nothing on the dashboard indicates *why* the phase is absent. If you want skipped phases visible on the run, return `PhaseResult.SKIP` from inside the phase instead: the phase row is recorded with its outcome.
* Runs of the same procedure with different flags produce phase sets that differ run-to-run, which is expected, but consider one procedure per station configuration (with its own [entry point](#multiple-procedures-one-codebase)) when the subsets are stable, so run analytics compare like with like.

## Offline upload

When the bench can lose connectivity, the CLI queues runs locally and uploads them when the network comes back. Each queued run keeps its original timestamp, so analytics stay accurate even when uploads land hours later. See [`tofupilot queue`](/docs/cli/queue).

## Python connector (self-managed)

For users running their own deployment pipeline who want to skip the TofuPilot CLI, the `tofupilot` Python package provides an OpenHTF output callback. You handle distribution and execution; TofuPilot just receives the run.

```bash title="install"
pip install "tofupilot[openhtf]"
```

```python title="main.py"
import openhtf as htf
from tofupilot.openhtf import upload

def phase_one(test):
    return htf.PhaseResult.CONTINUE

def main():
    test = htf.Test(
        phase_one,
        procedure_id="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",  # procedure UUID from the dashboard
        part_number="PCB01",
    )
    test.add_output_callbacks(upload())
    test.execute(lambda: "SN-0001")

if __name__ == "__main__":
    main()
```

`upload()` reads `TOFUPILOT_API_KEY` from the environment. `add_output_callbacks` is OpenHTF's own extension point, the same one `json_factory` uses, so the callback composes with any others you already have.

For a self-hosted instance, pass the server and — if it uses a private CA — the certificate:

```python
test.add_output_callbacks(upload(
    url="https://tofupilot.yourcompany.com",
    verify="/path/to/ca-certificate.pem",
))
```

<Callout type="warn">
  **The `TofuPilot` context manager is deprecated.** On `tofupilot>=2.16.0`, `with TofuPilot(test):` still works: it is a thin wrapper around this same callback, and using it emits a `FutureWarning`. Versions 2.15.0 to 2.15.2 do not include it and raise `ImportError` on the import. It will be removed in a future release, so migrate to the two lines above. The MQTT streaming it used to provide is gone: `stream=True` is accepted but ignored with a logged warning (pass `stream=False` to silence it), and the live operator UI is provided by the CLI. If you relied on it, run the test with [`tofupilot run`](/docs/cli/run) instead. OpenHTF is an extra since 2.15.0: install `tofupilot[openhtf]` rather than `tofupilot`, so a station that only uses the API client no longer pulls a test framework it never imports. One behavior difference when migrating off the wrapper: it suppresses `KeyboardInterrupt`, so Ctrl-C during a test exits without a traceback, while the plain callback lets Ctrl-C raise normally. Wrap `test.execute(...)` in `try/except KeyboardInterrupt` if your bench scripts depend on the quiet exit.
</Callout>

Uploading only needs the callback. To also get a live operator UI, deployment from Git, an offline queue and station management, use the CLI — it runs the same stock OpenHTF file with no TofuPilot code in it at all.

| Field          | Required | Description                                                                                                                                                                 |
| -------------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `procedure_id` | Yes      | Dashboard procedure UUID (find it on the procedure page). A legacy external identifier also works only if one was set on the procedure.                                     |
| `part_number`  | Yes      | Part number for the unit.                                                                                                                                                   |
| `revision`     | No       | Part revision. Defaults to `A`.                                                                                                                                             |
| `batch_number` | No       | Production batch.                                                                                                                                                           |
| `sub_units`    | No       | List of `{"serial_number": "..."}` for assembled units.                                                                                                                     |
| `operated_by`  | No       | Operator who ran the test. An email matching a member of your organization links the run to that account. Any other value is recorded verbatim as a declared operator name. |

For offline benches, write a JSON report during the run and upload it later from any machine with network access.

```python title="upload_later.py"
from openhtf.output.callbacks import json_factory
from tofupilot import TofuPilotClient

test.add_output_callbacks(json_factory.OutputToJSON("./report.json", indent=2))
test.execute(lambda: "SN-0001")

client = TofuPilotClient()
client.create_run_from_openhtf_report("./report.json")
```

## Supported OpenHTF versions

The CLI runs OpenHTF 1.3.0 (2018) through 1.6.1. Every release in that range
produces the same measurements, validators, attachments, phase outcomes and
dimensioned data on the dashboard.

| OpenHTF | Python | Notes                                       |
| ------- | ------ | ------------------------------------------- |
| 1.6.1   | 3.9+   | Current release.                            |
| 1.5.2   | 3.9+   |                                             |
| 1.4.4   | ≤ 3.9  | Uses `collections.Iterable`, moved in 3.10. |
| 1.3.0   | ≤ 3.10 |                                             |

Those Python ceilings come from OpenHTF itself, not from TofuPilot: 1.4.4 calls
`collections.Iterable`, moved to `collections.abc` in Python 3.10, and releases
before 1.5 call `inspect.getargspec`, removed in 3.11. Pin the interpreter in
your procedure's `pyproject.toml` when you stay on one of them — otherwise the
venv bootstrap picks a modern Python and the run fails inside OpenHTF's own
imports before your test starts:

```toml filename="pyproject.toml"
[project]
requires-python = ">=3.9,<3.11"
dependencies = ["openhtf==1.3.0", "setuptools<81"]
```

`setuptools<81` is needed for the same reason: OpenHTF below 1.5 imports
`pkg_resources`, which setuptools 81 removed.

Marginal limits (`marginal_minimum` / `marginal_maximum`) and `PhaseGroup` do
not exist before 1.5 and 1.4.4 respectively, so nothing is reported for them on
older releases. Everything else behaves identically.

## Unsupported features

The following OpenHTF features run as-is in Python, but TofuPilot does not surface them on the dashboard.

| Feature                                                                                      | Status                                                                                     |
| -------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| Monitors (`@openhtf.monitors(...)`)                                                          | Sampling thread runs, but emitted values are not captured.                                 |
| Diagnoses (`@PhaseDiagnoser`, `@TestDiagnoser`, `DiagnosesStore`, `Diagnosis`)               | Run as-is; `diagnosis_results`, `failure_diagnosis_results`, and `diagnosers` are dropped. |
| Conditional validators (`_ConditionalValidator`)                                             | Validator runs only when its diagnosis fires; not surfaced as a separate validator entry.  |
| Subtests (`Subtest`, `PhaseResult.FAIL_SUBTEST`)                                             | Phases execute, but subtest grouping and per-subtest outcomes are not persisted.           |
| Branches and checkpoints (`BranchSequence`, `PhaseFailureCheckpoint`, `DiagnosisCheckpoint`) | Control flow is honored; the branch / checkpoint nodes themselves are not persisted.       |
| `with_plugs` (plug-type substitution)                                                        | Runs as plain Python; no extra metadata on the dashboard.                                  |
| `FrontendAwareBasePlug` state                                                                | The plug runs, but its frontend state stream is ignored.                                   |
| `PhaseOptions` (`timeout_s`, `run_if`, `repeat_limit`, `force_repeat`, ...)                  | Honored by OpenHTF at runtime; the `options` object itself is not persisted on the phase.  |
| User-registered `output.callbacks` (`json_factory`, mfg-inspector, ...)                      | Still fire; TofuPilot does not consume the resulting files.                                |
| OpenHTF `output.servers.web_gui`                                                             | Use the TofuPilot dashboard instead.                                                       |
