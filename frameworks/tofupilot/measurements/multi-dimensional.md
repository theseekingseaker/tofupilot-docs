<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/measurements/multi-dimensional -->
# Multi-Dimensional
URL: /docs/frameworks/tofupilot/measurements/multi-dimensional

Record multi-dimensional measurement data with axis validators and aggregations for complex sensor and signal analysis in TofuPilot.

***

title: Multi-Dimensional
description: Record multi-dimensional measurement data with axis validators and aggregations for complex sensor and signal analysis in TofuPilot.
icon: ChartLine
---------------

import { Tabs, Tab, TabsList, TabsTrigger, TabsContent } from 'fumadocs-ui/components/tabs';
import { Callout } from 'fumadocs-ui/components/callout';
import { IconFileCode, IconBrandPython } from '@tabler/icons-react';
import { InlineOutcomeBadge } from '@/components/inline-outcome-badge';
import { MultiDimensionalMeasurementPreview } from '@/components/MultiDimensionalMeasurementPreview';

## Preview \[toc]

<MultiDimensionalMeasurementPreview
  name="Output Voltage"
  xAxis={{
  legend: "Time (ms)",
  data: [0, 5, 10, 15, 20, 25, 30, 35, 40, 45, 50, 55, 60, 65, 70, 75, 80, 85, 90, 95, 100, 105, 110, 115, 120, 125, 130, 135, 140, 145, 150, 155, 160, 165, 170, 175, 180, 185, 190, 195, 200]
}}
  yAxis={[
  {
    legend: "Voltage (V)",
    data: [0.03, 0.42, 1.15, 1.92, 2.58, 2.97, 3.08, 3.12, 3.07, 3.11, 3.09, 3.14, 3.08, 3.13, 3.10, 3.15, 3.09, 3.12, 3.11, 3.14, 3.08, 3.13, 3.10, 3.11, 3.09, 3.14, 3.07, 3.12, 3.10, 3.13, 3.09, 2.95, 2.61, 2.18, 1.72, 1.24, 0.78, 0.35, 0.18, 0.08, 0.02],
    color: "#0ea5e9"
  }
]}
  outcome="PASS"
/>

<div className="sr-only" aria-hidden="false">
  ```
  Multi-dimensional Measurement: Output Voltage
    X axis: Time (ms), 0..200
    Y axis: Voltage (V), rises 0->~3.1, holds ~3.1, falls ->0 (pulse waveform)
    Outcome: PASS
  ```
</div>

You can create a multi-dimensional measurement with `measurements`:

```yaml
measurements:
  - name: Output Voltage # [!code ++]
    title: Output Voltage # [!code ++]
    x_axis: # [!code ++]
      legend: Time # [!code ++]
      unit: ms # [!code ++]
    y_axis: # [!code ++]
      - legend: Voltage # [!code ++]
        unit: V # [!code ++]
```

You can set the measurement value in your Python phase using the builder pattern:

```python
def measure_voltage(measurements, dmm):
    times = []
    voltages = []

    for t in range(0, 201, 5):
        times.append(t)
        voltages.append(dmm.read_voltage())

    measurements.output_voltage.x_axis = times # [!code ++]
    measurements.output_voltage.y_axis.voltage = voltages # [!code ++]
```

## Definition

### Measurement

You can configure the measurement name and metadata.

| Property      | Type     | Required | Description                                            |
| ------------- | -------- | -------- | ------------------------------------------------------ |
| `key`         | `string` | No       | Unique identifier for the measurement within the phase |
| `name`        | `string` | Yes      | Display name (1-100 characters)                        |
| `description` | `string` | No       | Detailed description (max 50,000 characters)           |
| `title`       | `string` | No       | Chart title                                            |
| `x_axis`      | `object` | Yes      | X-axis specification                                   |
| `y_axis`      | `array`  | Yes      | Array of Y-axis specifications (one or more)           |

TofuPilot auto-generates the `key` from `name` (snake\_case) if not specified, and uses it for Python access (e.g., `measurements.frequency_response`).

### Axis

Each axis (x\_axis or y\_axis item) has the following properties:

| Property       | Type     | Required | Description                                                          |
| -------------- | -------- | -------- | -------------------------------------------------------------------- |
| `key`          | `string` | No       | Python identifier for axis (auto-generated from `legend` if not set) |
| `legend`       | `string` | No       | Axis label for chart legend                                          |
| `unit`         | `string` | No       | Axis unit (e.g., `"Hz"`, `"V"`, `"dB"`)                              |
| `description`  | `string` | No       | Axis description                                                     |
| `validators`   | `array`  | No       | Validators for axis data                                             |
| `aggregations` | `array`  | No       | Aggregations for axis data                                           |

TofuPilot matches the y axes your phase records to the declared ones by `key`. Series without a key are matched by position, when none of the recorded series has one. Mixing keyed and keyless series is reported in the phase log and nothing is matched: the declared validators don't apply, each series is checked only against the validators it carries itself.

A declared multi-dimensional measurement the phase never records is uploaded as an empty measurement. The limits declared on its axes and their aggregations are carried on the measurement itself, <InlineOutcomeBadge outcome="fail" />, so the run page shows which limit had no value to meet. The same goes for a single declared axis the phase left unrecorded: an unrecorded y axis keeps its failed limits beside the x axis, and an unrecorded x axis carries them on the measurement.

## Validators

You can add validators to each axis to check individual data points.

```yaml
y_axis:
  - legend: Voltage
    unit: V
    validators: # [!code ++]
      - operator: "==" # [!code ++]
        expected_value: [3.0, 3.1, 3.0, 3.1] # [!code ++]
```

Axis validators only support the `==` and `!=` operators with an array of expected values matching the data length. TofuPilot will compare each data point against the corresponding expected value and set the outcome to <InlineOutcomeBadge outcome="fail" /> if any point fails.

Any other operator (`<=`, `>`, `in`, …) can't judge a series. The validator is left <InlineOutcomeBadge outcome="unset" />, the measurement with it, and the phase log names the operator. Put that kind of limit on an [aggregation](#aggregations) instead.

## Aggregations

You can add aggregations to each axis to validate statistical properties.

```yaml
y_axis:
  - legend: Voltage
    unit: V
    aggregations: # [!code ++]
      - type: mean # [!code ++]
        validators: # [!code ++]
          - operator: ">=" # [!code ++]
            expected_value: 2.9 # [!code ++]
          - operator: "<=" # [!code ++]
            expected_value: 3.1 # [!code ++]
```

TofuPilot will validate the aggregation value against the specified criteria. You must set the aggregation value from Python. See [Aggregations](/docs/frameworks/tofupilot/measurements/aggregations) for details.

The measurement outcome comes from the validators on every axis and on their aggregations: <InlineOutcomeBadge outcome="fail" /> if any fails or has no data to check, <InlineOutcomeBadge outcome="pass" /> if all passed, <InlineOutcomeBadge outcome="unset" /> if there are none or one couldn't run. See [Outcome](/docs/frameworks/tofupilot/measurements#outcome).

## Examples

### Multi-Channel Waveform

We will capture waveforms from multiple channels simultaneously:

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>

    <TabsTrigger value="capture_waveform.py">
      <IconBrandPython className="size-4" />

      capture\_waveform.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    name: Oscilloscope Capture Test

    plugs:
      - key: scope
        name: Oscilloscope
        python: plugs.scope:Scope

    main:
      - name: Waveform Capture
        python: capture_waveform
        measurements:
          - name: Output Waveform
            description: Dual-channel oscilloscope capture
            title: Output Waveform
            x_axis:
              legend: Time
              unit: ms
            y_axis:
              - legend: Channel 1
                key: channel_1
                unit: V
              - legend: Channel 2
                key: channel_2
                unit: V
    ```
  </TabsContent>

  <TabsContent value="capture_waveform.py">
    ```python
    def capture_waveform(measurements, log, scope):
        log.info("Capturing waveform")

        scope.trigger()
        data = scope.get_waveform()

        measurements.output_waveform.x_axis = data['time']
        measurements.output_waveform.y_axis.channel_1 = data['channel1']
        measurements.output_waveform.y_axis.channel_2 = data['channel2']

        log.info(f"Captured {len(data['time'])} samples")
    ```
  </TabsContent>
</Tabs>

### Combining Validators and Aggregations

We will measure voltage over time and validate both individual readings and overall statistics:

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
    name: Power Supply Test

    plugs:
      - key: dmm
        name: Digital Multimeter
        python: plugs.dmm:Dmm

    main:
      - name: Voltage Monitoring
        python: measure_voltage
        measurements:
          - name: Output Voltage
            description: Voltage monitoring with range and statistical validation
            title: Output Voltage
            x_axis:
              legend: Time
              unit: ms
            y_axis:
              - legend: Voltage
                unit: V
                aggregations:
                  - type: min
                    validators:
                      - operator: ">="
                        expected_value: 0
                  - type: max
                    validators:
                      - operator: "<="
                        expected_value: 5
                  - type: mean
                    validators:
                      - operator: ">="
                        expected_value: 2.9
                      - operator: "<="
                        expected_value: 3.1
    ```
  </TabsContent>

  <TabsContent value="measure_voltage.py">
    ```python
    def measure_voltage(measurements, log, dmm):
        log.info("Monitoring voltage over time")

        times = []
        voltages = []

        for t in range(0, 201, 5):
            times.append(t)
            voltages.append(dmm.read_voltage())

        measurements.output_voltage.x_axis = times
        measurements.output_voltage.y_axis.voltage = voltages

        aggs = measurements.output_voltage.y_axis.voltage.aggregations
        aggs.min = min(voltages)
        aggs.max = max(voltages)
        aggs.mean = sum(voltages) / len(voltages)

        log.info(f"Captured {len(times)} samples")
    ```
  </TabsContent>
</Tabs>

Range validation (0-5V) is done via `min`/`max` aggregations since axis validators only support `==` and `!=`. The mean aggregation validates overall statistics (2.9-3.1V). The measurement fails if any aggregation validator fails.
