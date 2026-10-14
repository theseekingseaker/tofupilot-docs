<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/measurements/aggregations -->
# Aggregations
URL: /docs/frameworks/tofupilot/measurements/aggregations

Validate statistics on measurement data with custom aggregation types like average, standard deviation, and Cpk in TofuPilot.

***

title: Aggregations
description: Validate statistics on measurement data with custom aggregation types like average, standard deviation, and Cpk in TofuPilot.
icon: Sigma
-----------

import { Callout } from 'fumadocs-ui/components/callout';
import { InlineOutcomeBadge } from '@/components/inline-outcome-badge';
import { AggregationPreview } from '@/components/AggregationPreview';

## Overview \[toc]

<AggregationPreview />

<div className="sr-only" aria-hidden="false">
  ```
  Aggregation Preview: Motor Torque
    Multi-dimensional measurement (Angle deg vs Torque Nm, sinusoidal)
    Aggregation: mean across all samples, shown as a mean line
    Outcome: PASS
  ```
</div>

You can use aggregations to validate statistics on measurement data. Define the aggregation structure in YAML and set the values from Python.

```yaml
measurements:
  - name: Temperature
    unit: °C
    aggregations: # [!code ++]
      - type: mean # [!code ++]
        validators: # [!code ++]
          - operator: ">=" # [!code ++]
            expected_value: 20 # [!code ++]
```

You set the measurement value and the aggregation value from Python:

```python
def measure_temperature(measurements, log, sensor):
    temperatures = []

    for _ in range(60):
        temperatures.append(sensor.read_temperature())

    measurements.temperature = temperatures # [!code ++]
    measurements.temperature.aggregations.mean = ( # [!code ++]
        sum(temperatures) / len(temperatures) # [!code ++]
    ) # [!code ++]
```

TofuPilot will validate the aggregation value against the specified criteria.

## Definition

You can configure the aggregation type and validation.

| Property     | Type     | Required | Description                                                           |
| ------------ | -------- | -------- | --------------------------------------------------------------------- |
| `type`       | `string` | Yes      | Aggregation type name (any string, e.g., `"mean"`, `"percentile_95"`) |
| `unit`       | `string` | No       | Unit for aggregated result                                            |
| `validators` | `array`  | No       | Validators to apply to aggregated value                               |

The `type` field is a free string — you can use any name that describes your aggregation. Common conventions include `mean`, `min`, `max`, `sum`, `std`, `count`, but you can use any custom name.

TofuPilot will validate the aggregation value and set the outcome to <InlineOutcomeBadge outcome="pass" /> if validators are met, <InlineOutcomeBadge outcome="fail" /> if not. An aggregation with validators but no value set from Python is <InlineOutcomeBadge outcome="fail" />. An aggregation without validators is <InlineOutcomeBadge outcome="unset" />.

Aggregation validators count toward the measurement outcome: any failure, including a validator on an aggregation with no value, makes it <InlineOutcomeBadge outcome="fail" />. A declared aggregation with no value and no validators is uploaded empty and leaves the measurement <InlineOutcomeBadge outcome="unset" />, even if its other validators passed. See [Outcome](/docs/frameworks/tofupilot/measurements#outcome).

## Examples

### Temperature Stability

You can validate temperature stability using mean and standard deviation aggregations:

```yaml
name: Thermal Characterization

plugs:
  - key: sensor
    name: Temperature Sensor
    python: plugs.sensor:Sensor

main:
  - name: Temperature Stability Test
    python: measure_temperature
    measurements:
      - name: Temperature
        unit: °C
        description: Operating temperature over 60 second test
        validators:
          - operator: "<="
            expected_value: 85
        aggregations:
          - type: mean
            validators:
              - operator: ">="
                expected_value: 20
              - operator: "<="
                expected_value: 80
          - type: std
            validators:
              - operator: "<="
                expected_value: 2.0
```

You set the measurement value and aggregation values from Python:

```python
import time
import numpy as np

def measure_temperature(measurements, log, sensor):
    temperatures = []

    for _ in range(60):
        temp = sensor.read_temperature()
        temperatures.append(temp)
        time.sleep(1)

    measurements.temperature = temperatures
    measurements.temperature.aggregations.mean = float(np.mean(temperatures))
    measurements.temperature.aggregations.std = float(np.std(temperatures))
```

TofuPilot will validate each aggregation value against its validators and set the measurement outcome.

### Resistance Statistics

You can validate the average resistance across multiple test points:

```yaml
name: Sensor Board Test

plugs:
  - key: dmm
    name: Digital Multimeter
    python: plugs.dmm:Dmm

main:
  - name: Resistance Test
    python: measure_resistance
    measurements:
      - name: Resistance
        unit: Ω
        description: Resistance measurements across 10 test points
        validators:
          - operator: ">="
            expected_value: 95
          - operator: "<="
            expected_value: 105
        aggregations:
          - type: mean
            unit: Ω
            validators:
              - operator: ">="
                expected_value: 98
              - operator: "<="
                expected_value: 102
          - type: std
            validators:
              - operator: "<="
                expected_value: 1.5
```

You set the measurement value and aggregation values from Python:

```python
import numpy as np

def measure_resistance(measurements, log, dmm):
    resistances = []

    for point in range(10):
        r = dmm.measure_resistance(point)
        resistances.append(r)
        log.info(f"Point {point}: {r:.2f} Ω")

    measurements.resistance = resistances
    measurements.resistance.aggregations.mean = float(np.mean(resistances))
    measurements.resistance.aggregations.std = float(np.std(resistances))
```

TofuPilot will validate each aggregation value against its validators and set the measurement outcome.

### Multiple Statistics

You can define multiple aggregations on the same measurement:

```yaml
name: Power Supply Test

plugs:
  - key: multimeter
    name: Multimeter
    python: plugs.multimeter:Multimeter

main:
  - name: Voltage Monitoring
    python: monitor_voltage
    measurements:
      - name: Voltage Samples
        unit: V
        aggregations:
          - type: mean
            validators:
              - operator: ">="
                expected_value: 4.9
              - operator: "<="
                expected_value: 5.1
          - type: min
            validators:
              - operator: ">="
                expected_value: 4.8
          - type: max
            validators:
              - operator: "<="
                expected_value: 5.2
          - type: std
            validators:
              - operator: "<="
                expected_value: 0.05
```

You set the measurement value and aggregation values from Python:

```python
import time
import numpy as np

def monitor_voltage(measurements, log, multimeter):
    samples = []

    for _ in range(100):
        v = multimeter.read_voltage()
        samples.append(v)
        time.sleep(0.1)

    measurements.voltage_samples = samples
    measurements.voltage_samples.aggregations.mean = float(np.mean(samples))
    measurements.voltage_samples.aggregations.min = float(np.min(samples))
    measurements.voltage_samples.aggregations.max = float(np.max(samples))
    measurements.voltage_samples.aggregations.std = float(np.std(samples))
```

TofuPilot will validate each aggregation value against its validators and set the measurement outcome.
