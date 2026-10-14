<!-- Source: https://www.tofupilot.com/docs/frameworks/robot-framework -->
# Robot Framework on TofuPilot
URL: /docs/frameworks/robot-framework

Learn how to run a Robot Framework suite through TofuPilot so test cases become phases and Measure keywords preserve limits end to end.

***

title: Robot Framework on TofuPilot
sidebarTitle: Robot Framework
description: "Learn how to run a Robot Framework suite through TofuPilot so test cases become phases and Measure keywords preserve limits end to end."
------------------------------------------------------------------------------------------------------------------------------------------------------

Robot Framework is a keyword-driven test framework that organizes execution as `.robot` suites of test cases composed from reusable keywords.

With TofuPilot, you can deploy Robot Framework suites to your stations via Git push and stream live test data to both the kiosk and the dashboard, with zero configuration.

## Getting started

To get started with Robot Framework on TofuPilot:

* Clone our Robot Framework starter template to your favorite Git provider and deploy it on TofuPilot.

<TemplateCards filter={['tofupilot/template-robot-starter']} />

* If you have a Robot Framework suite, push it to your Git provider and import it from the **New Procedure** wizard.

## Integration

The TofuPilot CLI runs your Robot Framework suite natively via the Listener API v3 and streams test cases, measurements, and logs to the dashboard. A bundled keyword library (`tofupilot_robot`) ships embedded in the CLI and exposes measurement keywords that preserve limits across the listener boundary, which is the one piece you cannot get from Robot's built-in `BuiltIn` library.

```text title="suite.robot"
*** Settings ***
Library    tofupilot_robot

*** Test Cases ***
Supply Voltage Is Stable
    ${voltage}=    Read Voltage
    Measure Numeric    voltage    ${voltage}    min=4.8    max=5.2    unit=V
```

### Test cases

In Robot Framework, every `*** Test Cases ***` entry is one unit of execution. TofuPilot maps each test case to a `phase` row.

| Robot Framework        | TofuPilot               | Notes                                                      |
| ---------------------- | ----------------------- | ---------------------------------------------------------- |
| test case name         | `name`                  | Capped at 200 chars.                                       |
| test status            | `outcome`               | `PASS` / `FAIL` / `SKIP`. Errors collapse to `ERROR`.      |
| test documentation     | `docstring`             | From the `[Documentation]` setting. Capped at 50000 chars. |
| start / end time       | `startedAt` / `endedAt` | ISO 8601 timestamps.                                       |
| template / data-driven | `retryCount`            | Each iteration becomes a separate phase row, not a retry.  |

### Measurements

The bundled `tofupilot_robot` library exposes three keywords that validate a runtime value, fail the test on violation, and emit a structured measurement payload.

| Keyword           | Use for                                             |
| ----------------- | --------------------------------------------------- |
| `Measure Numeric` | Numeric value with optional `min` / `max` / `unit`. |
| `Measure String`  | String with `expected=` or `matches=` (regex).      |
| `Measure Boolean` | Boolean with optional `expected=`.                  |

```text title="suite.robot"
*** Test Cases ***
Firmware Version
    ${version}=    Get Firmware
    Measure String    firmware_version    ${version}    expected=1.4.2
```

| Robot keyword argument        | TofuPilot         | Notes                                                                            |
| ----------------------------- | ----------------- | -------------------------------------------------------------------------------- |
| `name`                        | `name`            | Capped at 200 chars.                                                             |
| `value`                       | `value`           | Routed to `numeric_measurement`, `string_measurement`, or `boolean_measurement`. |
| `min` / `max`                 | validator entries | Each emitted as `>=` / `<=` on the measurement.                                  |
| `expected` (string / boolean) | validator entry   | Equality check.                                                                  |
| `matches` (string)            | validator entry   | Regex match.                                                                     |
| `unit`                        | `units`           | Capped at 60 chars.                                                              |
| `description`                 | `docstring`       | Capped at 50000 chars.                                                           |

Robot's `BuiltIn` library expands variables before the listener sees keyword calls, so `Should Be Within Range` loses the literal limits before they reach TofuPilot. Use the `Measure *` keywords for any value you want to land on the dashboard with limits intact.

### Logs

Robot's `Log` keyword and Python `logging` records flow through the listener.

```text title="suite.robot"
*** Test Cases ***
Calibration
    Log    Calibration started
```

| Robot log   | TofuPilot    | Notes                                            |
| ----------- | ------------ | ------------------------------------------------ |
| `level`     | `level`      | `DEBUG`, `INFO`, `WARNING`, `ERROR`, `CRITICAL`. |
| `timestamp` | `timestamp`  | ISO 8601 timestamp.                              |
| `message`   | `message`    | Capped at 50000 chars.                           |
| source file | `sourceFile` | Capped at 200 chars.                             |
| source line | `lineNumber` |                                                  |

Logs are stored at the run level; per-test association is not preserved.

### Run metadata

Robot Framework has no built-in concept of a unit under test. TofuPilot reads defaults from `pyproject.toml` to identify the run:

```toml title="pyproject.toml"
[tool.tofupilot]
serial_number = "SN-0001"
part_number = "PCB01"
revision_number = "A"
batch_number = "2024-001"
operated_by = "jane.doe@example.com"
auto_identify = false
```

`operated_by` attributes the run to an operator. An email matching a member of your organization links the run to that account. Any other value is recorded verbatim as a declared operator name.

When `auto_identify` is `false` (default), the CLI prompts the operator for the serial number on stdin before collection. When `true`, the defaults are used directly — the right choice for stations where a barcode scanner fills the serial in advance.

## Entry point

By default Robot discovers `.robot` suites across the whole procedure directory. To narrow a run to a subdirectory or a single suite, for example when several procedures share one codebase, set the procedure's [entry point](/docs/cli/procedures#--entry-point) to that path (`suites/eol` or `suites/functional.robot`).

## Offline upload

When the bench can lose connectivity, the CLI queues runs locally and uploads them when the network comes back. Each queued run keeps its original timestamp, so analytics stay accurate even when uploads land hours later. See [`tofupilot queue`](/docs/cli/queue).

## Unsupported features

The following Robot Framework features run as-is, but TofuPilot does not surface them on the dashboard.

| Feature                                         | Status                                                                      |
| ----------------------------------------------- | --------------------------------------------------------------------------- |
| `Should Be ...` keywords from `BuiltIn`         | Run as assertions; the literal limits are lost before reaching TofuPilot.   |
| Test tags                                       | Honored by Robot for selection; not persisted on the phase.                 |
| `Setup` / `Teardown` keywords                   | Honored by Robot at runtime; not surfaced as separate phases.               |
| External listeners                              | Run alongside the TofuPilot listener; their output is not consumed.         |
| Robot test selection (`-t` / `--test`)          | The connector runs the whole directory.                                     |
| Operator prompts                                | Not supported. Use OpenHTF or the TofuPilot Framework for live operator UI. |
| Multi-dim measurements with per-axis validators | TofuPilot Framework only.                                                   |
| Attachments                                     | Not supported. Use the SDK directly to attach files.                        |
