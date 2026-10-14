<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/measurements/string -->
# String
URL: /docs/frameworks/tofupilot/measurements/string

Record string measurement values with regex pattern validation for firmware versions, serial formats, and labels in TofuPilot.

***

title: String
description: Record string measurement values with regex pattern validation for firmware versions, serial formats, and labels in TofuPilot.
icon: Type
----------

import { Tabs, Tab, TabsList, TabsTrigger, TabsContent } from 'fumadocs-ui/components/tabs';
import { Callout } from 'fumadocs-ui/components/callout';
import { IconFileCode, IconBrandPython } from '@tabler/icons-react';
import { MeasurementPreview } from '@/components/MeasurementPreview';
import { InlineOutcomeBadge } from '@/components/inline-outcome-badge';

## Preview \[toc]

<MeasurementPreview
  name="Error Message"
  value="Invalid config"
  validators={[
  { operator: 'matches', expectedValue: '^OK$' }
]}
  outcome="FAIL"
/>

<div className="sr-only" aria-hidden="false">
  ```
  String Measurement: Error Message
    Value: "Invalid config"
    Validator: matches ^OK$
    Outcome: FAIL ("Invalid config" does not match ^OK$)
  ```
</div>

You can create a string measurement with `measurements`:

```yaml
measurements:
  - name: Error Message # [!code ++]
    validators: # [!code ++]
      - operator: matches # [!code ++]
        expected_value: "^OK$" # [!code ++]
```

You can set the measurement value in your Python phase:

```python
def check_status(run, measurements, device):
    message = device.get_error_message()
    measurements.error_message = message # [!code ++]
```

## Definition

You can configure the measurement name and metadata.

| Property      | Type     | Required | Description                                                 |
| ------------- | -------- | -------- | ----------------------------------------------------------- |
| `key`         | `string` | No       | Unique identifier for the measurement within the phase      |
| `name`        | `string` | Yes      | Display name (1-100 characters)                             |
| `unit`        | `string` | No       | Measurement unit (e.g., `"sn"`, `"ver"`, max 50 characters) |
| `description` | `string` | No       | Detailed description (max 50,000 characters)                |

TofuPilot auto-generates the `key` from `name` (snake\_case) if not specified, and uses it for Python access (e.g., `measurements.serial_number`). The `unit` and `description` display in the web interface and reports.

## Validators

You can add one or several validators to your measurement using the following properties:

| Property         | Type                | Required | Description                        |
| ---------------- | ------------------- | -------- | ---------------------------------- |
| `operator`       | `string`            | Yes      | Comparison operator                |
| `expected_value` | `string` or `array` | Yes      | Expected value(s) or regex pattern |

You can use the following operators for string measurements:

| Operator  | Expected Value   | Description                    | Example                                |
| --------- | ---------------- | ------------------------------ | -------------------------------------- |
| `==`      | String           | Value must equal expected      | `expected_value: "PASS"`               |
| `!=`      | String           | Value must not equal expected  | `expected_value: "ERROR"`              |
| `in`      | Array of strings | Value must be in the array     | `expected_value: ["v1.0.0", "v1.0.1"]` |
| `not in`  | Array of strings | Value must not be in the array | `expected_value: ["FAIL", "ERROR"]`    |
| `matches` | Regex pattern    | Value must match regex pattern | `expected_value: "^SN[0-9]{8}$"`       |

TofuPilot evaluates validators and sets the outcome to <InlineOutcomeBadge outcome="pass" /> if met, <InlineOutcomeBadge outcome="fail" /> if not, or if no value was provided, and <InlineOutcomeBadge outcome="unset" /> if there are no validators. See [Outcome](/docs/frameworks/tofupilot/measurements#outcome).

## Examples

### Pattern Matching with Regex

We will validate error messages match expected pattern:

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>

    <TabsTrigger value="check_errors.py">
      <IconBrandPython className="size-4" />

      check\_errors.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    name: Firmware Validation

    plugs:
      - key: device
        name: Device
        python: plugs.device:Device

    main:
      - name: Error Message Check
        python: phases.check_errors
        measurements:
          - name: Error Message
            description: Error message from device diagnostic
            validators:
              - operator: matches
                expected_value: "^(OK|PASS|SUCCESS)$"
    ```
  </TabsContent>

  <TabsContent value="check_errors.py">
    ```python
    def check_errors(log, measurements, device):
        log.info("Checking device error message")

        message = device.get_error_message()
        measurements.error_message = message

        if not message in ["OK", "PASS", "SUCCESS"]:
            log.warning(f"Unexpected message: {message}")
    ```
  </TabsContent>
</Tabs>

### Status Code Validation

We will validate device status codes with array matching:

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>

    <TabsTrigger value="check_status.py">
      <IconBrandPython className="size-4" />

      check\_status.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    name: Device Status Test

    plugs:
      - key: device
        name: Device
        python: plugs.device:Device

    main:
      - name: Status Check
        python: phases.check_status
        measurements:
          - name: Device Status
            description: Device operational status code
            validators:
              - operator: in
                expected_value: ["OK", "READY", "IDLE"]
              - operator: not in
                expected_value: ["ERROR", "FAULT", "FAILED"]
    ```
  </TabsContent>

  <TabsContent value="check_status.py">
    ```python
    def check_status(log, measurements, device):
        log.info("Reading device status")

        status = device.get_status()
        measurements.device_status = status

        if status in ["ERROR", "FAULT", "FAILED"]:
            log.error(f"Device in error state: {status}")
    ```
  </TabsContent>
</Tabs>
