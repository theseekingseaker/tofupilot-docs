<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/measurements/json -->
# JSON
URL: /docs/frameworks/tofupilot/measurements/json

Record structured JSON object and array measurement values to capture rich device state, config snapshots, and diagnostics in TofuPilot procedures.

***

title: JSON
description: Record structured JSON object and array measurement values to capture rich device state, config snapshots, and diagnostics in TofuPilot procedures.
icon: Braces
------------

import { Tabs, Tab, TabsList, TabsTrigger, TabsContent } from 'fumadocs-ui/components/tabs';
import { Callout } from 'fumadocs-ui/components/callout';
import { IconFileCode, IconBrandPython } from '@tabler/icons-react';
import { JsonMeasurementPreview } from '@/components/JsonMeasurementPreview';
import { InlineOutcomeBadge } from '@/components/inline-outcome-badge';

## Preview \[toc]

<JsonMeasurementPreview
  name="Operating Mode"
  value={{
  mode: 'active',
  power: 'high',
}}
  outcome="PASS"
/>

<div className="sr-only" aria-hidden="false">
  ```
  JSON Measurement: Operating Mode
    Value: { "mode": "active", "power": "high" }
    Validator: x == { "mode": "active", "power": "high" }
    Outcome: PASS
  ```
</div>

You can create a JSON measurement with `measurements`:

```yaml
measurements:
  - name: Operating Mode # [!code ++]
    validators: # [!code ++]
      - operator: "==" # [!code ++]
        expected_value: # [!code ++]
          mode: active # [!code ++]
          power: high # [!code ++]
```

You can set the measurement value in your Python phase by assigning a `dict` or `list`:

```python
def check_mode(measurements):
    measurements.operating_mode = {"mode": "active", "power": "high"} # [!code ++]
```

TofuPilot detects the JSON type from the value. Any `dict`, or any `list` that is not a numeric matrix, is stored as a JSON measurement. A numeric matrix (a `list` of equal-length numeric `list`s) is stored as a [multi-dimensional](/docs/frameworks/tofupilot/measurements/multi-dimensional) measurement instead.

## Definition

You can configure the measurement name and metadata.

| Property      | Type     | Required | Description                                            |
| ------------- | -------- | -------- | ------------------------------------------------------ |
| `key`         | `string` | No       | Unique identifier for the measurement within the phase |
| `name`        | `string` | Yes      | Display name (1-100 characters)                        |
| `unit`        | `string` | No       | Measurement unit (max 50 characters)                   |
| `description` | `string` | No       | Detailed description (max 50,000 characters)           |

TofuPilot auto-generates the `key` from `name` (snake\_case) if not specified, and uses it for Python access (e.g., `measurements.operating_mode`). The `unit` and `description` display in the web interface and reports.

## Validators

You can add one or several validators to your measurement using the following properties:

| Property         | Type                | Required | Description                        |
| ---------------- | ------------------- | -------- | ---------------------------------- |
| `operator`       | `string`            | Yes      | Comparison operator                |
| `expected_value` | `object` or `array` | Yes      | Expected JSON value for comparison |

A JSON object has no single comparable scalar, so JSON measurements compare the whole value. You can use the following operators for JSON measurements:

| Operator | Expected Value  | Description                             | Example                                                      |
| -------- | --------------- | --------------------------------------- | ------------------------------------------------------------ |
| `==`     | Object or array | Value must equal expected               | `expected_value: { "mode": "active" }`                       |
| `!=`     | Object or array | Value must not equal expected           | `expected_value: {}`                                         |
| `in`     | Array of values | Value must be one of the allowed values | `expected_value: [{ "mode": "active" }, { "mode": "idle" }]` |
| `not in` | Array of values | Value must not be one of the values     | `expected_value: [{ "mode": "error" }]`                      |

TofuPilot evaluates validators and sets the outcome to <InlineOutcomeBadge outcome="pass" /> if met, <InlineOutcomeBadge outcome="fail" /> if not, or if no value was provided, and <InlineOutcomeBadge outcome="unset" /> if there are no validators. See [Outcome](/docs/frameworks/tofupilot/measurements#outcome).

To validate a single field, extract it in your Python phase and record it as a typed [numeric](/docs/frameworks/tofupilot/measurements/numeric), [string](/docs/frameworks/tofupilot/measurements/string), or [boolean](/docs/frameworks/tofupilot/measurements/boolean) measurement.

## Examples

### Mode Check

We will read the device mode object and verify it matches the expected configuration:

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>

    <TabsTrigger value="check_mode.py">
      <IconBrandPython className="size-4" />

      check\_mode.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    name: Device Configuration Test

    main:
      - name: Mode Check
        python: phases.check_mode
        measurements:
          - name: Operating Mode
            description: Active operating mode reported by the device
            validators:
              - operator: "=="
                expected_value:
                  mode: active
                  power: high
    ```
  </TabsContent>

  <TabsContent value="check_mode.py">
    ```python
    def check_mode(log, measurements):
        log.info("Reading operating mode")

        measurements.operating_mode = {
            "mode": "active",
            "power": "high",
        }
    ```
  </TabsContent>
</Tabs>

### Configuration Snapshot

We will capture the full device configuration for traceability, with no validation:

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>

    <TabsTrigger value="capture_config.py">
      <IconBrandPython className="size-4" />

      capture\_config.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    name: Camera Module Test

    plugs:
      - key: device
        name: Device
        python: plugs.device:Device

    main:
      - name: Capture Config
        python: phases.capture_config
        measurements:
          - name: Device Config
            description: Full configuration snapshot read from the device
    ```
  </TabsContent>

  <TabsContent value="capture_config.py">
    ```python
    def capture_config(log, measurements, device):
        log.info("Reading device configuration")

        measurements.device_config = device.dump_config()
    ```
  </TabsContent>
</Tabs>
