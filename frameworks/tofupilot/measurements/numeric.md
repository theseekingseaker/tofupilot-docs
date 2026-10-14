<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/measurements/numeric -->
# Numeric
URL: /docs/frameworks/tofupilot/measurements/numeric

Record numeric measurement values with minimum and maximum range validation for hardware test parameters in TofuPilot procedures.

***

title: Numeric
description: Record numeric measurement values with minimum and maximum range validation for hardware test parameters in TofuPilot procedures.
icon: Hash
----------

import { MeasurementPreview } from '@/components/MeasurementPreview';
import { Callout } from 'fumadocs-ui/components/callout';
import { Tabs, Tab, TabsList, TabsTrigger, TabsContent } from 'fumadocs-ui/components/tabs';
import { IconFileCode, IconBrandPython } from '@tabler/icons-react';
import { InlineOutcomeBadge } from '@/components/inline-outcome-badge';

## Preview \[toc]

<MeasurementPreview
  name="Output Voltage"
  value={4.5}
  unit="V"
  validators={[
  { operator: '>=', expectedValue: 4.8 },
  { operator: '<=', expectedValue: 5.2 }
]}
  outcome="FAIL"
/>

<div className="sr-only" aria-hidden="false">
  ```
  Numeric Measurement: Output Voltage
    Value: 4.5 V
    Validators: 4.8 <= x <= 5.2
    Outcome: FAIL (4.5 < 4.8)
  ```
</div>

You can create a numeric measurement with `measurements`:

```yaml
measurements:
  - name: Output Voltage # [!code ++]
    unit: V # [!code ++]
    validators: # [!code ++]
      - operator: ">=" # [!code ++]
        expected_value: 4.8 # [!code ++]
      - operator: "<=" # [!code ++]
        expected_value: 5.2 # [!code ++]
```

You can set the measurement value in your Python phase:

```python
def measure_voltage(run, measurements, multimeter):
    voltage = multimeter.read_voltage()
    measurements.output_voltage = voltage # [!code ++]
```

## Definition

You can configure the measurement name and metadata.

| Property      | Type     | Required | Description                                                      |
| ------------- | -------- | -------- | ---------------------------------------------------------------- |
| `key`         | `string` | No       | Unique identifier for the measurement within the phase           |
| `name`        | `string` | Yes      | Display name (1-100 characters)                                  |
| `unit`        | `string` | No       | Measurement unit (e.g., `"V"`, `"A"`, `"°C"`, max 50 characters) |
| `description` | `string` | No       | Detailed description (max 50,000 characters)                     |

TofuPilot auto-generates the `key` from `name` (snake\_case) if not specified, and uses it for Python access (e.g., `measurements.output_voltage`). The `unit` and `description` display in the web interface and reports.

## Validators

You can add one or several validators to your measurement using the following properties:

| Property         | Type                | Required | Description                      |
| ---------------- | ------------------- | -------- | -------------------------------- |
| `operator`       | `string`            | Yes      | Comparison operator              |
| `expected_value` | `number` or `array` | Yes      | Expected value(s) for comparison |

You can use the following operators for numeric measurements:

| Operator | Expected Value   | Description                         | Example                             |
| -------- | ---------------- | ----------------------------------- | ----------------------------------- |
| `==`     | Number           | Value must equal expected           | `expected_value: 5.0`               |
| `!=`     | Number           | Value must not equal expected       | `expected_value: 0.0`               |
| `>`      | Number           | Value must be greater than          | `expected_value: 4.8`               |
| `>=`     | Number           | Value must be greater than or equal | `expected_value: 4.8`               |
| `<`      | Number           | Value must be less than             | `expected_value: 5.2`               |
| `<=`     | Number           | Value must be less than or equal    | `expected_value: 5.2`               |
| `in`     | Array of numbers | Value must be in the array          | `expected_value: [5.0, 12.0, 24.0]` |
| `not in` | Array of numbers | Value must not be in the array      | `expected_value: [0.0, -1.0]`       |

TofuPilot evaluates validators and sets the outcome to <InlineOutcomeBadge outcome="pass" /> if met, <InlineOutcomeBadge outcome="fail" /> if not, or if no value was provided, and <InlineOutcomeBadge outcome="unset" /> if there are no validators. See [Outcome](/docs/frameworks/tofupilot/measurements#outcome).

You can use [Aggregations](/docs/frameworks/tofupilot/measurements/aggregations) for statistical validation on numeric arrays.

## Examples

### Voltage Measurement

We will measure output voltage with min/max limits:

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
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    name: Battery Functional Test

    plugs:
      - key: multimeter
        name: Multimeter
        python: plugs.multimeter:Multimeter

    main:
      - name: Power Supply Test
        python: phases.measure_voltage
        measurements:
          - name: Output Voltage
            unit: V
            description: Main power supply output voltage
            validators:
              - operator: ">="
                expected_value: 4.8
              - operator: "<="
                expected_value: 5.2
    ```
  </TabsContent>

  <TabsContent value="measure_voltage.py">
    ```python
    def measure_voltage(log, measurements, multimeter):
        log.info("Measuring output voltage")

        voltage = multimeter.read_voltage()
        measurements.output_voltage = voltage

        if voltage < 4.8 or voltage > 5.2:
            log.warning(f"Voltage out of spec: {voltage} V")
    ```
  </TabsContent>
</Tabs>

### Set Point Validation

We will validate voltage is one of the allowed setpoints:

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>

    <TabsTrigger value="check_voltage.py">
      <IconBrandPython className="size-4" />

      check\_voltage.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    name: Regulator Board Test

    plugs:
      - key: multimeter
        name: Multimeter
        python: plugs.multimeter:Multimeter

    main:
      - name: Voltage Check
        python: phases.check_voltage
        measurements:
          - name: Output Voltage
            unit: V
            description: Verify output matches one of the valid setpoints
            validators:
              - operator: in
                expected_value: [5.0, 12.0, 24.0]
    ```
  </TabsContent>

  <TabsContent value="check_voltage.py">
    ```python
    def check_voltage(log, measurements, multimeter):
        log.info("Checking voltage setpoint")

        voltage = multimeter.read_voltage()
        measurements.output_voltage = voltage

        if voltage not in [5.0, 12.0, 24.0]:
            log.warning(f"Unexpected voltage: {voltage} V")
    ```
  </TabsContent>
</Tabs>
