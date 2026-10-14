<!-- Source: https://www.tofupilot.com/docs/frameworks/pytest -->
# Pytest on TofuPilot
URL: /docs/frameworks/pytest

Learn how to run an existing pytest suite through TofuPilot so each test becomes a phase and asserts promote to measurements automatically.

***

title: Pytest on TofuPilot
sidebarTitle: Pytest
description: "Learn how to run an existing pytest suite through TofuPilot so each test becomes a phase and asserts promote to measurements automatically."
----------------------------------------------------------------------------------------------------------------------------------------------------------

pytest is a code-first Python test framework built around `test_*` functions and `assert` statements, with hardware setup handled via fixtures.

With TofuPilot, you can deploy pytest suites to your stations via Git push and stream live test data to both the kiosk and the dashboard, with zero configuration.

## Getting started

To get started with pytest on TofuPilot:

* Clone our pytest starter template to your favorite Git provider and deploy it on TofuPilot.

<TemplateCards filter={['tofupilot/template-pytest-starter']} />

* If you have a pytest suite, push it to your Git provider and import it from the **New Procedure** wizard.

## Integration

The TofuPilot CLI runs your pytest suite natively under an embedded plugin and streams tests, asserts, and logs to the dashboard.

```python title="test_supply.py"
def test_supply_voltage():
    voltage = read_voltage()
    assert 4.8 <= voltage <= 5.2, "Supply voltage [V]"
```

### Tests

In pytest, every collected `test_*` function is one unit of execution. TofuPilot maps each test to a `phase` row.

| pytest                    | TofuPilot               | Notes                                                                       |
| ------------------------- | ----------------------- | --------------------------------------------------------------------------- |
| test function name        | `name`                  | Capped at 200 chars.                                                        |
| test outcome              | `outcome`               | `passed` → `PASS`, `failed` → `FAIL`, `skipped` → `SKIP`, errors → `ERROR`. |
| test docstring            | `docstring`             | Capped at 50000 chars.                                                      |
| call time                 | `startedAt` / `endedAt` | Wall-clock around the call phase only.                                      |
| `pytest.mark.parametrize` | `retryCount`            | Each parametrize instance is a separate phase row, not a retry.             |

### Measurements

The plugin parses each test's AST at collection time and promotes recognized `assert` patterns to typed measurements with limits and a runtime-captured value.

```python title="test_temperature.py"
def test_temperature():
    temp = read_temperature()
    assert 20 <= temp <= 80, "Case temperature [°C]"
```

| Assert pattern                                           | TofuPilot                                              |
| -------------------------------------------------------- | ------------------------------------------------------ |
| `assert lo <= x <= hi`                                   | Numeric measurement with `>=` / `<=` validator entries |
| `assert x >= lo` / `<= hi` / `> lo` / `< hi`             | Numeric measurement, single bound                      |
| `assert x == n` / `!= n` (numeric)                       | Numeric measurement, equality                          |
| `assert x == pytest.approx(n, ...)`                      | Numeric measurement, equality with tolerance           |
| `assert s == "value"` / `!= "value"`                     | String measurement, equality                           |
| `assert s in [...]` / `not in [...]`                     | String measurement, set membership                     |
| `assert b is True` / `is False` / `== True` / `== False` | Boolean measurement                                    |
| Anything else                                            | not persisted — phase still records pass/fail          |

The assert message becomes the measurement `docstring`. Append `[unit]` to set the `units` field on numeric measurements.

### Logs

Standard Python `logging` records flow through TofuPilot. The connector attaches a handler to the root logger at session start, so any module-level call is captured.

```python title="test_calibration.py"
import logging

def test_calibration():
    logging.info("Calibration started")
    assert calibrate() == "OK"
```

| Python `LogRecord`   | TofuPilot     | Notes                                            |
| -------------------- | ------------- | ------------------------------------------------ |
| `levelname`          | `level`       | `DEBUG`, `INFO`, `WARNING`, `ERROR`, `CRITICAL`. |
| `created`            | `timestamp`   | Epoch seconds → ISO 8601 timestamp.              |
| `message`            | `message`     | Capped at 50000 chars.                           |
| `pathname`           | `sourceFile`  | Capped at 200 chars.                             |
| `lineno`             | `lineNumber`  |                                                  |
| `name` (logger name) | not persisted |                                                  |

Logs are stored at the run level; per-test association is not preserved.

### Run metadata

pytest has no built-in concept of a unit under test. TofuPilot reads defaults from `pyproject.toml` to identify the run:

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

### Fixtures

pytest fixtures run as-is in Python — `setup`, `teardown`, scoping, and dependency injection work unchanged. The dashboard does not surface fixture instances or lifecycle events; only the data emitted from inside a test (measurements, logs) is captured.

## Entry point

By default pytest discovers `test_*.py` files across the whole procedure directory. To narrow a run to a subdirectory or a single file, for example when several procedures share one codebase, set the procedure's [entry point](/docs/cli/procedures#--entry-point) to that path (`tests/smoke` or `tests/test_supply.py`). pytest then only collects from there.

## Offline upload

When the bench can lose connectivity, the CLI queues runs locally and uploads them when the network comes back. Each queued run keeps its original timestamp, so analytics stay accurate even when uploads land hours later. See [`tofupilot queue`](/docs/cli/queue).

## Unsupported features

The following pytest features run as-is in Python, but TofuPilot does not surface them on the dashboard.

| Feature                                             | Status                                                                                            |
| --------------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| `pytest.mark.parametrize`                           | Each instance becomes a separate phase row; parametrize metadata is not persisted.                |
| `pytest.mark.skipif` / `xfail`                      | Honored by pytest; skipped tests record as `SKIP`, xfail outcomes collapse to standard pass/fail. |
| Custom markers                                      | Run as-is; not persisted on the phase.                                                            |
| Fixtures (`@pytest.fixture`)                        | Run as-is; no dashboard surface.                                                                  |
| `conftest.py` hooks                                 | Honored by pytest; not surfaced.                                                                  |
| pytest plugins (`pytest-xdist`, `pytest-html`, ...) | Load as normal; TofuPilot does not consume their output.                                          |
| Attachments                                         | No pytest helper; attach via the SDK directly if needed.                                          |
| Operator prompts                                    | Not supported. Use OpenHTF or the TofuPilot Framework for live operator UI.                       |
| Multi-dim measurements with per-axis validators     | TofuPilot Framework only.                                                                         |
