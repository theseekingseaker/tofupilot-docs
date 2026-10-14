<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/phases -->
# Phases
URL: /docs/frameworks/tofupilot/phases

Break down your TofuPilot test into distinct phases that run Python functions, shell commands, and operator UI interactions.

***

title: Phases
description: Break down your TofuPilot test into distinct phases that run Python functions, shell commands, and operator UI interactions.
icon: ListChecks
----------------

import { DynamicCodeBlock } from '@/components/themed-dynamic-code-block';
import { ImageZoom } from 'fumadocs-ui/components/image-zoom';
import { Tabs, Tab, TabsList, TabsTrigger, TabsContent } from 'fumadocs-ui/components/tabs';
import { Callout } from 'fumadocs-ui/components/callout';
import { PhaseFlow } from '@/components/phase-flow';
import { OutcomeFlow } from '@/components/outcome-flow';
import { InlineOutcomeBadge } from '@/components/inline-outcome-badge';
import { TestPhaseBadges } from '@/components/test-phase-badge';
import { File, Folder, Files } from 'fumadocs-ui/components/files';
import { IconFileCode, IconBrandPython, IconTerminal } from '@tabler/icons-react';

## Overview \[toc]

A phase represents a single step within your test procedure.

## Runtime

Each phase can run Python scripts, execute a command, and/or display operator UI prompts. Find usage and example for [Python](/docs/frameworks/tofupilot/phases/python), [shell commands](/docs/frameworks/tofupilot/phases/executable) and [operator UI](/docs/frameworks/tofupilot/operator-ui).

## Name

You can create a new phase by adding it to the `main` list.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    main:
      - name: Power On Test # [!code ++]
    ```
  </TabsContent>
</Tabs>

TofuPilot will automatically trim whitespace and enforce a 100 character limit.

## Description

You can add an optional description to explain what the phase does.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    main:
      - name: Power On Test
        description: "Verify 5V power rail is within specification" # [!code ++]
    ```
  </TabsContent>
</Tabs>

TofuPilot will enforce a 1,000 character limit on descriptions.

## Key

You can define a key for referencing this phase in your Python phases or YAML procedure.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    main:
      - name: Power On Test
        description: "Verify 5V power rail is within specification"
        key: power_on_test # [!code ++]
    ```
  </TabsContent>
</Tabs>

If not specified, TofuPilot auto-generates a key from the phase name. Keys must be valid Python identifiers: start with a letter or underscore, followed by letters, numbers, or underscores.

## Timeout

You can set a maximum duration for the phase with `timeout: 90s`. When it elapses, the phase ends with the <InlineOutcomeBadge outcome="timeout" /> outcome. Without it, the phase has no time limit. See [Timeout](/docs/frameworks/tofupilot/phases/timeout) for the duration syntax, bounds, and per-runtime behavior.

## Outcome

Your phase can end with one of these outcomes:

| Outcome                                  | If                                                                                        |
| ---------------------------------------- | ----------------------------------------------------------------------------------------- |
| <InlineOutcomeBadge outcome="error" />   | Unhandled exception thrown                                                                |
| <InlineOutcomeBadge outcome="skip" />    | Called `phase.skip()` or phase skipped by framework                                       |
| <InlineOutcomeBadge outcome="timeout" /> | Phase exceeded timeout limit                                                              |
| <InlineOutcomeBadge outcome="aborted" /> | Called `phase.stop()`, or the run was stopped by the operator while the phase was running |
| <InlineOutcomeBadge outcome="fail" />    | Called `phase.fail()`, `phase.retry()` exceeding retry limit, or measurements invalid     |
| <InlineOutcomeBadge outcome="pass" />    | All measurements valid                                                                    |

### Outcome Control

You can control phase outcomes by calling methods on the `phase` parameter in your python function :

| Method          | Description                                                                                                                                             |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `phase.fail()`  | Mark phase as fail and exit immediately                                                                                                                 |
| `phase.skip()`  | Skip this phase                                                                                                                                         |
| `phase.retry()` | Retry this phase (see [Retry](/docs/frameworks/tofupilot/phases/retry))                                                                                 |
| `phase.stop()`  | Stop the slot this phase runs in: its remaining phases are skipped, its teardown runs. With a single slot, that is the whole run. Other slots continue. |

For example, you can skip a phase when a precondition is not met:

```python
def advanced_calibration(phase, device):
    if not device.supports_advanced_mode():
        phase.skip() # [!code ++]

    # Phase continues if not skipped
```

### Outcome Resolution

TofuPilot evaluates outcomes in priority order (first match wins):

<OutcomeFlow
  nodes={[
{ id: 'phase-start', type: 'start', position: { x: 180, y: 0 }, data: { label: 'Phase Start' } },
{ id: 'skip', type: 'check', position: { x: 150, y: 100 }, data: { label: 'Call skip?' } },
{ id: 'error', type: 'check', position: { x: 150, y: 200 }, data: { label: 'Threw exception?' } },
{ id: 'outcome-error', type: 'outcomeGroup', position: { x: 420, y: 200 }, data: { outcomes: ['error'] } },
{ id: 'outcome-skip', type: 'outcomeGroup', position: { x: 420, y: 100 }, data: { outcomes: ['skip'] } },
{ id: 'timeout', type: 'check', position: { x: 150, y: 300 }, data: { label: 'Reach timeout?' } },
{ id: 'outcome-timeout', type: 'outcomeGroup', position: { x: 420, y: 300 }, data: { outcomes: ['timeout'] } },
{ id: 'aborted', type: 'check', position: { x: 150, y: 400 }, data: { label: 'Call stop?' } },
{ id: 'outcome-aborted', type: 'outcomeGroup', position: { x: 420, y: 400 }, data: { outcomes: ['aborted'] } },
{ id: 'retry', type: 'check', position: { x: 150, y: 500 }, data: { label: 'Call retry?' } },
{ id: 'fail', type: 'check', position: { x: 150, y: 600 }, data: { label: 'Call fail?' } },
{ id: 'outcome-fail-1', type: 'outcomeGroup', position: { x: 510, y: 500 }, data: { outcomes: ['fail'] } },
{ id: 'outcome-fail-2', type: 'outcomeGroup', position: { x: 420, y: 600 }, data: { outcomes: ['fail'] } },
{ id: 'measurements', type: 'check', position: { x: 150, y: 700 }, data: { label: 'Valid measurements?' } },
{ id: 'outcome-measurement-pass', type: 'outcomeGroup', position: { x: 420, y: 660 }, data: { outcomes: ['pass'] } },
{ id: 'outcome-measurement-fail', type: 'outcomeGroup', position: { x: 420, y: 740 }, data: { outcomes: ['fail'] } },
{ id: 'next-action', type: 'end', position: { x: 180, y: 800 }, data: { label: 'Next Action' } },
]}
  edges={[
  // Start → Skip
  { id: 'e-start-skip', source: 'phase-start', target: 'skip' },

  // Skip
  { id: 'e-skip-outcome', source: 'skip', target: 'outcome-skip', sourceHandle: 'right', label: 'Yes' },
  { id: 'e-skip-error', source: 'skip', target: 'error', label: 'No' },

  // Error
  { id: 'e-error-outcome', source: 'error', target: 'outcome-error', sourceHandle: 'right', label: 'Yes' },
  { id: 'e-error-timeout', source: 'error', target: 'timeout', label: 'No' },

  // Timeout
  { id: 'e-timeout-outcome', source: 'timeout', target: 'outcome-timeout', sourceHandle: 'right', label: 'Yes' },
  { id: 'e-timeout-aborted', source: 'timeout', target: 'aborted', label: 'No' },

  // Aborted
  { id: 'e-aborted-outcome', source: 'aborted', target: 'outcome-aborted', sourceHandle: 'right', label: 'Yes' },
  { id: 'e-aborted-retry', source: 'aborted', target: 'retry', label: 'No' },

  // 🔁 Retry:
  // 1) No → Fail (check node)
  {
    id: 'e-retry-fail-no',
    source: 'retry',
    target: 'fail',
    label: 'No',
  },

  // 2) Yes & retry_count < retry_limit → Phase Start
  {
    id: 'e-retry-start-yes-under-limit',
    source: 'retry',
    target: 'phase-start',
    sourceHandle: 'left',
    targetHandle: 'left',
    label: 'Yes & count < limit',
  },

  // 3) Yes & count >= limit → Fail badge (outcome-fail)
  {
    id: 'e-retry-outcome-fail-yes-maxed',
    source: 'retry',
    target: 'outcome-fail-1',
    sourceHandle: 'right',
    label: 'Yes & count >= limit',
  },

  // Fail
  { id: 'e-fail-outcome', source: 'fail', target: 'outcome-fail-2', sourceHandle: 'right', label: 'Yes' },
  { id: 'e-fail-measurements', source: 'fail', target: 'measurements', label: 'No' },

  // Measurements
  {
    id: 'e-measurements-pass',
    source: 'measurements',
    target: 'outcome-measurement-pass',
    sourceHandle: 'right',
    label: 'Yes',
  },
  {
    id: 'e-measurements-fail',
    source: 'measurements',
    target: 'outcome-measurement-fail',
    sourceHandle: 'right',
    label: 'No',
  },
  { id: 'e-measurements-next', source: 'measurements', target: 'next-action' },
]}
/>

After the outcome is determined, TofuPilot moves to the [Next Action](#next-action).

## Example

Let's create a battery test with 2 phases:

1. Test power that succeeds
2. Test capacity that fails

We'll create the procedure file and the Python modules:

<Files>
  <File name="procedure.yaml" />

  <Folder name="phases" defaultOpen>
    <File name="test_power.py" />

    <File name="test_capacity.py" />
  </Folder>
</Files>

We'll define the phases and reference the modules:

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>

    <TabsTrigger value="test_power.py">
      <IconBrandPython className="size-4" />

      test\_power.py
    </TabsTrigger>

    <TabsTrigger value="test_capacity.py">
      <IconBrandPython className="size-4" />

      test\_capacity.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="test_power.py">
    ```python
    def test_power(phase, battery):
        if not battery.check_voltage():
            phase.fail()
        # Implicit pass
    ```
  </TabsContent>

  <TabsContent value="test_capacity.py">
    ```python
    def test_capacity(phase, battery):
        if not battery.verify_capacity():
            phase.fail()
        # Implicit pass
    ```
  </TabsContent>

  <TabsContent value="procedure.yaml">
    ```yaml
    name: Battery Functional Test

    plugs:
      - key: battery
        name: Battery Tester
        python: plugs.battery:Battery

    main:
      - name: Test Power
        python: phases.test_power

      - name: Test Capacity
        python: phases.test_capacity
    ```
  </TabsContent>
</Tabs>

TofuPilot executes both phases in parallel:

<PhaseFlow
  nodes={[
  { id: 'start', type: 'start', position: { x: 250, y: 0 }, data: { label: 'Start' } },
  { id: 'test_power', type: 'phase', position: { x: 0, y: 120 }, data: { label: 'Test Power', outcome: 'pass' } },
  { id: 'test_capacity', type: 'phase', position: { x: 320, y: 120 }, data: { label: 'Test Capacity', outcome: 'fail' } },
  { id: 'end', type: 'end', position: { x: 250, y: 240 }, data: { label: 'End' } },
]}
  edges={[
  { id: 'e-start-power', source: 'start', target: 'test_power' },
  { id: 'e-start-capacity', source: 'start', target: 'test_capacity' },
  { id: 'e-power-end', source: 'test_power', target: 'end' },
  { id: 'e-capacity-end', source: 'test_capacity', target: 'end' },
]}
/>

## Previous Results

A phase can access measurements, outcome, and duration from a previously completed phase. Name a function parameter after the phase's `key`, and TofuPilot injects a read-only result object.

<Callout type="warn">
  The referenced phase must have completed before the current phase runs. Use `depends_on` to guarantee execution order.
</Callout>

### Example

A `measure_voltage` phase records a voltage measurement, then a `verify_voltage` phase reads it using the phase key:

<Files>
  <File name="procedure.yaml" />

  <Folder name="phases" defaultOpen>
    <File name="measure_voltage.py" />

    <File name="verify_voltage.py" />
  </Folder>
</Files>

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>

    <TabsTrigger value="measure_voltage.py">
      <IconBrandPython className="size-4" />

      measure\_voltage.py
    </TabsTrigger>

    <TabsTrigger value="verify_voltage.py">
      <IconBrandPython className="size-4" />

      verify\_voltage.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    main:
      - name: Measure Voltage
        key: measure_voltage # [!code ++]
        python: phases.measure_voltage
        measurements:
          - name: voltage
            key: voltage # [!code ++]

      - name: Verify Voltage
        key: verify_voltage # [!code ++]
        python: phases.verify_voltage
        depends_on:
          - measure_voltage
    ```
  </TabsContent>

  <TabsContent value="measure_voltage.py">
    ```python
    def measure_voltage(measurements):
        measurements.voltage = 5.03
    ```
  </TabsContent>

  <TabsContent value="verify_voltage.py">
    ```python
    def verify_voltage(phase, measure_voltage): # [!code ++]
        voltage = measure_voltage.voltage # [!code ++]
        if voltage < 4.9 or voltage > 5.1:
            phase.fail()
    ```
  </TabsContent>
</Tabs>

<Callout>
  If you don't specify a `key`, it is auto-generated from the phase name (e.g., "Measure Voltage" becomes `measure_voltage`).
</Callout>

### Available Properties

| Property                         | Description                              |
| -------------------------------- | ---------------------------------------- |
| `<phase_key>.<measurement_name>` | Value of a recorded measurement          |
| `<phase_key>.outcome`            | Phase outcome (`"pass"`, `"fail"`, etc.) |
| `<phase_key>.duration_ms`        | Phase execution time in milliseconds     |

<Callout>
  In multi-slot runs, a phase can only access results from phases within the same slot.
</Callout>

## Next Action

After determining the phase outcome, TofuPilot decides the next action. You can customize the default behavior at [procedure level](/docs/frameworks/tofupilot/execution/callbacks) or at phase level.

### Actions

| Action     | Description                                                             |
| ---------- | ----------------------------------------------------------------------- |
| `continue` | Continue to next phase                                                  |
| `stop`     | Stop entire run                                                         |
| `retry`    | Retry this phase (see [Retry](/docs/frameworks/tofupilot/phases/retry)) |

### Default

| Outcome                                  | Next Action                                                                         |
| ---------------------------------------- | ----------------------------------------------------------------------------------- |
| <InlineOutcomeBadge outcome="pass" />    | `continue` to next phase                                                            |
| <InlineOutcomeBadge outcome="fail" />    | `stop` entire run (see [Callbacks](/docs/frameworks/tofupilot/execution/callbacks)) |
| <InlineOutcomeBadge outcome="skip" />    | `continue` to next phase                                                            |
| <InlineOutcomeBadge outcome="error" />   | `stop` entire run                                                                   |
| <InlineOutcomeBadge outcome="timeout" /> | `stop` entire run                                                                   |
| <InlineOutcomeBadge outcome="aborted" /> | `stop` entire run                                                                   |

### Per-phase Override

Use the `then` field to override the default action for a specific phase. Map any of `pass`, `fail`, `error`, `timeout` to one of the [actions](#actions) above.

For example, if `on_first_failure: stop` globally but you want execution to continue past a non-critical phase that errors:

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>

    <TabsTrigger value="diagnostic_check.py">
      <IconBrandPython className="size-4" />

      diagnostic\_check.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    execution:
      on_first_failure: stop

    plugs:
      - key: sensor
        name: Sensor
        python: plugs.sensor:Sensor

    main:
      - name: Diagnostic Check
        python: diagnostic_check
        then: # [!code ++]
          error: continue # [!code ++]
          fail: continue # [!code ++]
      - name: Final Report
        python: final_report
        depends_on:
          - diagnostic_check
    ```
  </TabsContent>

  <TabsContent value="diagnostic_check.py">
    ```python
    def diagnostic_check(phase, sensor):
        reading = sensor.read()
        if reading is None:
            phase.fail("Sensor returned no data")
        if reading < 0:
            raise ValueError(f"Invalid sensor reading: {reading}")
    ```
  </TabsContent>
</Tabs>
