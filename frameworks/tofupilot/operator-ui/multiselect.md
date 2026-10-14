<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/operator-ui/multiselect -->
# Multi-Select
URL: /docs/frameworks/tofupilot/operator-ui/multiselect

Prompt the operator to select multiple options from a dropdown list during test execution for multi-criteria input in TofuPilot.

***

title: Multi-Select
description: Prompt the operator to select multiple options from a dropdown list during test execution for multi-criteria input in TofuPilot.
icon: ChevronsDown
------------------

import { IsolatedUIPreview } from '@/components/IsolatedUIPreview';
import { Callout } from 'fumadocs-ui/components/callout';

## Preview \[toc]

<IsolatedUIPreview
  type="multiselect"
  label="Test Interfaces"
  description="Select all interfaces to test"
  required={true}
  options={[
  { label: "USB", value: "usb" },
  { label: "Ethernet", value: "ethernet" }
]}
/>

<div className="sr-only" aria-hidden="false">
  ```
  Operator UI: Multiselect (dropdown, multi)
    Label: Test Interfaces (required)
    Options: USB | Ethernet
    Help: Select all interfaces to test
  ```
</div>

You can create a multi-select with `multiselect`:

```yaml
ui:
  components:
    - key: test_interfaces # [!code ++]
      type: multiselect # [!code ++]
      label: "Test Interfaces" # [!code ++]
      description: "Select all interfaces to test" # [!code ++]
      required: true # [!code ++]
      options: # [!code ++]
        - label: "USB" # [!code ++]
          value: "usb" # [!code ++]
        - label: "Ethernet" # [!code ++]
          value: "ethernet" # [!code ++]
```

## Properties

### Display

You can configure how the multi-select appears to the operator.

| Property      | Type     | Required | Description                                  |
| ------------- | -------- | -------- | -------------------------------------------- |
| `key`         | `string` | Yes      | Unique identifier for accessing the value    |
| `type`        | `string` | Yes      | Must be `"multiselect"`                      |
| `label`       | `string` | No       | Display label for the multi-select           |
| `description` | `string` | No       | Helper text displayed below the multi-select |

### Value

You can set default values and bind to measurements or unit properties.

| Property        | Type     | Required | Description                                                                                                                                |
| --------------- | -------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| `default_value` | `array`  | No       | Array of default selected option values                                                                                                    |
| `bind`          | `string` | No       | Bind to unit property or measurement. See [Bind to Measurements](/docs/frameworks/tofupilot/operator-ui#bind-to-measurements) for details. |

### Options

You can define the list of choices available for selection.

| Property  | Type    | Required | Description                                                                                                                                                               |
| --------- | ------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `options` | `array` | No       | Array of objects with `label` and `value` properties. Leave it out to fill the list at run time with [`ui.set`](/docs/frameworks/tofupilot/operator-ui#update-properties) |

Each option object has:

| Property | Type     | Description                    |
| -------- | -------- | ------------------------------ |
| `label`  | `string` | Text displayed to the operator |
| `value`  | `string` | Value submitted when selected  |

### Validation

You can validate selection before the value is submitted.

| Property   | Type      | Required | Description                                                                |
| ---------- | --------- | -------- | -------------------------------------------------------------------------- |
| `required` | `boolean` | No       | If true, operator must select at least one option (shows "Required" badge) |

TofuPilot validates operator input before submission, preventing continuation until all constraints are met. For some workflows, this input validation can create workarounds where operators bypass the UI to proceed.

Instead, you can capture the input as a Measurement using [Binding](/docs/frameworks/tofupilot/operator-ui#bind-to-measurements) and validate it with [Validators](/docs/frameworks/tofupilot/measurements). TofuPilot will record the actual operator input while enforcing pass/fail criteria.

## Examples

### Equipment Selection

We'll let operators select required equipment from a large list:

```yaml
main:
  - name: Verify Equipment
    ui:
      components:
        - key: required_equipment
          type: multiselect
          label: "Required Equipment"
          description: "Select all equipment needed" # [!code ++]
          required: true
          options: # [!code ++]
            - label: "Multimeter" # [!code ++]
              value: "multimeter" # [!code ++]
            - label: "Oscilloscope" # [!code ++]
              value: "oscilloscope" # [!code ++]
            - label: "Power Supply" # [!code ++]
              value: "power_supply" # [!code ++]
            - label: "Signal Generator" # [!code ++]
              value: "signal_gen" # [!code ++]
            - label: "Load Bank" # [!code ++]
              value: "load_bank" # [!code ++]
            - label: "Thermal Camera" # [!code ++]
              value: "thermal_cam" # [!code ++]
```

### Feature Flags

We'll allow operators to enable optional test features:

```yaml
main:
  - name: Configure Test Features
    ui:
      components:
        - key: enabled_features
          type: multiselect
          label: "Enable Features"
          required: false
          options: # [!code ++]
            - label: "Advanced Logging" # [!code ++]
              value: "logging" # [!code ++]
            - label: "Performance Metrics" # [!code ++]
              value: "metrics" # [!code ++]
            - label: "Video Recording" # [!code ++]
              value: "video" # [!code ++]
            - label: "Auto-retry on Failure" # [!code ++]
              value: "auto_retry" # [!code ++]
            - label: "Email Notifications" # [!code ++]
              value: "email" # [!code ++]
```
