<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/operator-ui/number-input -->
# Number Input
URL: /docs/frameworks/tofupilot/operator-ui/number-input

Prompt the operator to enter a numeric value during test execution using a validated number input field in TofuPilot.

***

title: Number Input
description: Prompt the operator to enter a numeric value during test execution using a validated number input field in TofuPilot.
icon: Hash
----------

import { IsolatedUIPreview } from '@/components/IsolatedUIPreview';
import { Callout } from 'fumadocs-ui/components/callout';

## Preview \[toc]

<IsolatedUIPreview type="number_input" label="Voltage" placeholder="3.3" description="Enter the measured voltage" required={true} />

<div className="sr-only" aria-hidden="false">
  ```
  Operator UI: Number Input
    Label: Voltage (required)
    Placeholder: 3.3
    Help: Enter the measured voltage
  ```
</div>

You can create a number input with `number_input`:

```yaml
ui:
  components:
    - key: voltage # [!code ++]
      type: number_input # [!code ++]
      label: "Voltage" # [!code ++]
      placeholder: "3.3" # [!code ++]
      description: "Enter the measured voltage" # [!code ++]
      required: true # [!code ++]
```

## Properties

### Display

You can configure how the input field appears to the operator.

| Property      | Type     | Required | Description                               |
| ------------- | -------- | -------- | ----------------------------------------- |
| `key`         | `string` | Yes      | Unique identifier for accessing the value |
| `type`        | `string` | Yes      | Must be `"number_input"`                  |
| `label`       | `string` | No       | Display label for the input               |
| `description` | `string` | No       | Helper text displayed below the input     |
| `placeholder` | `string` | No       | Hint text shown when empty                |

### Value

You can set default values and bind to measurements or unit properties.

| Property        | Type     | Required | Description                                                                                                                                |
| --------------- | -------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| `default_value` | `number` | No       | Default value to pre-fill the input field                                                                                                  |
| `bind`          | `string` | No       | Bind to unit property or measurement. See [Bind to Measurements](/docs/frameworks/tofupilot/operator-ui#bind-to-measurements) for details. |

### Modifiers

You can configure numeric increment behavior.

| Property | Type     | Required | Description                                   |
| -------- | -------- | -------- | --------------------------------------------- |
| `step`   | `number` | No       | Increment step for +/- buttons (default: `1`) |

### Validation

You can validate input range before the value is submitted.

| Property   | Type      | Required | Description                                                   |
| ---------- | --------- | -------- | ------------------------------------------------------------- |
| `required` | `boolean` | No       | If true, operator must provide input (shows "Required" badge) |
| `min`      | `number`  | No       | Minimum allowed value                                         |
| `max`      | `number`  | No       | Maximum allowed value                                         |

A phase can change `min`, `max` and `step` while the screen is open, for example bounds read from the product configuration. See [Update Properties](/docs/frameworks/tofupilot/operator-ui#update-properties).

TofuPilot validates operator input before submission, preventing continuation until all constraints are met. For some workflows, this input validation can create workarounds where operators bypass the UI to proceed.

Instead, you can capture the input as a Measurement using [Binding](/docs/frameworks/tofupilot/operator-ui#bind-to-measurements) and validate it with [Validators](/docs/frameworks/tofupilot/measurements). TofuPilot will record the actual operator input while enforcing pass/fail criteria.

## Examples

### Range Validation

We'll enforce minimum and maximum value limits:

```yaml
main:
  - name: Measure Voltage
    ui:
      components:
        - key: voltage
          type: number_input
          label: "Voltage"
          min: 3.0 # [!code ++]
          max: 3.6 # [!code ++]
          required: true
```

### Decimal Precision

We'll configure decimal places and step increment:

```yaml
main:
  - name: Collect Temperature
    ui:
      components:
        - key: temperature
          type: number_input
          label: "Temperature"
          placeholder: "25.0"
          step: 0.1 # [!code ++]
          required: true
```
