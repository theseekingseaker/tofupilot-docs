<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/measurements/boolean -->
# Boolean
URL: /docs/frameworks/tofupilot/measurements/boolean

Record boolean measurement values with pass/fail equality validation for connectivity and presence checks in TofuPilot procedures.

***

title: Boolean
description: Record boolean measurement values with pass/fail equality validation for connectivity and presence checks in TofuPilot procedures.
icon: ToggleLeft
----------------

import { Tabs, Tab, TabsList, TabsTrigger, TabsContent } from 'fumadocs-ui/components/tabs';
import { Callout } from 'fumadocs-ui/components/callout';
import { IconFileCode, IconBrandPython } from '@tabler/icons-react';
import { MeasurementPreview } from '@/components/MeasurementPreview';
import { InlineOutcomeBadge } from '@/components/inline-outcome-badge';

## Preview \[toc]

<MeasurementPreview
  name="Device Connected"
  value={false}
  validators={[
  { operator: '==', expectedValue: true }
]}
  outcome="FAIL"
/>

<div className="sr-only" aria-hidden="false">
  ```
  Boolean Measurement: Device Connected
    Value: false
    Validator: x == true
    Outcome: FAIL (false != true)
  ```
</div>

You can create a boolean measurement with `measurements`:

```yaml
measurements:
  - name: Device Connected # [!code ++]
    validators: # [!code ++]
      - operator: "==" # [!code ++]
        expected_value: true # [!code ++]
```

You can set the measurement value in your Python phase:

```python
def check_connection(run, measurements, device):
    connected = device.is_connected()
    measurements.device_connected = connected # [!code ++]
```

## Definition

You can configure the measurement name and metadata.

| Property      | Type     | Required | Description                                            |
| ------------- | -------- | -------- | ------------------------------------------------------ |
| `key`         | `string` | No       | Unique identifier for the measurement within the phase |
| `name`        | `string` | Yes      | Display name (1-100 characters)                        |
| `unit`        | `string` | No       | Measurement unit (max 50 characters)                   |
| `description` | `string` | No       | Detailed description (max 50,000 characters)           |

TofuPilot auto-generates the `key` from `name` (snake\_case) if not specified, and uses it for Python access (e.g., `measurements.device_connected`). The `unit` and `description` display in the web interface and reports.

## Validators

You can add one or several validators to your measurement using the following properties:

| Property         | Type      | Required | Description            |
| ---------------- | --------- | -------- | ---------------------- |
| `operator`       | `string`  | Yes      | Comparison operator    |
| `expected_value` | `boolean` | Yes      | Expected boolean value |

You can use the following operators for boolean measurements:

| Operator | Expected Value | Description                   | Example                 |
| -------- | -------------- | ----------------------------- | ----------------------- |
| `==`     | Boolean        | Value must equal expected     | `expected_value: true`  |
| `!=`     | Boolean        | Value must not equal expected | `expected_value: false` |

TofuPilot evaluates validators and sets the outcome to <InlineOutcomeBadge outcome="pass" /> if met, <InlineOutcomeBadge outcome="fail" /> if not, or if no value was provided, and <InlineOutcomeBadge outcome="unset" /> if there are no validators. See [Outcome](/docs/frameworks/tofupilot/measurements#outcome).

## Examples

### Connection Verification

We will verify device connection is established:

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>

    <TabsTrigger value="verify_connection.py">
      <IconBrandPython className="size-4" />

      verify\_connection.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    name: Board Bring-Up

    plugs:
      - key: device
        name: Device
        python: plugs.device:Device

    main:
      - name: Connection Test
        python: phases.verify_connection
        measurements:
          - name: Device Connected
            description: Verify USB connection to device
            validators:
              - operator: "=="
                expected_value: true
    ```
  </TabsContent>

  <TabsContent value="verify_connection.py">
    ```python
    def verify_connection(log, measurements, device):
        log.info("Checking device connection")

        connected = device.is_connected()
        measurements.device_connected = connected

        if not connected:
            log.error("Device not connected")
    ```
  </TabsContent>
</Tabs>

### Multiple Boolean Checks

We will check multiple device states in one phase:

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
      - name: Error Check
        python: phases.check_errors
        measurements:
          - name: Error State Active
            description: Check if device is reporting errors
            validators:
              - operator: "!="
                expected_value: true

          - name: System Ready
            description: Check if device is ready for testing
            validators:
              - operator: "=="
                expected_value: true
    ```
  </TabsContent>

  <TabsContent value="check_errors.py">
    ```python
    def check_errors(log, measurements, device):
        log.info("Checking device error state")

        error_active = device.has_errors()
        measurements.error_state_active = error_active

        if error_active:
            log.error("Device reporting errors")

        ready = device.is_ready()
        measurements.system_ready = ready

        if not ready:
            log.warning("Device not ready")
    ```
  </TabsContent>
</Tabs>
