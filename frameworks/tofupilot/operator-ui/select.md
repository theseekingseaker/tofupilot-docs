<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/operator-ui/select -->
# Select
URL: /docs/frameworks/tofupilot/operator-ui/select

Prompt the operator to choose one option from a dropdown list during test execution for configuration or classification in TofuPilot.

***

title: Select
description: Prompt the operator to choose one option from a dropdown list during test execution for configuration or classification in TofuPilot.
icon: ChevronDown
-----------------

import { IsolatedUIPreview } from '@/components/IsolatedUIPreview';
import { Callout } from 'fumadocs-ui/components/callout';

## Preview \[toc]

<IsolatedUIPreview
  type="select"
  label="Power Supply Voltage"
  description="Select the test voltage"
  required={true}
  options={[
  { label: "3.3V", value: "3.3" },
  { label: "5.0V", value: "5.0" }
]}
/>

<div className="sr-only" aria-hidden="false">
  ```
  Operator UI: Select (dropdown, single)
    Label: Power Supply Voltage (required)
    Options: 3.3V | 5.0V
    Help: Select the test voltage
  ```
</div>

You can create a select with `select`:

```yaml
ui:
  components:
    - key: voltage # [!code ++]
      type: select # [!code ++]
      label: "Power Supply Voltage" # [!code ++]
      description: "Select the test voltage" # [!code ++]
      required: true # [!code ++]
      options: # [!code ++]
        - label: "3.3V" # [!code ++]
          value: "3.3" # [!code ++]
        - label: "5.0V" # [!code ++]
          value: "5.0" # [!code ++]
```

## Properties

### Display

You can configure how the select appears to the operator.

| Property      | Type     | Required | Description                               |
| ------------- | -------- | -------- | ----------------------------------------- |
| `key`         | `string` | Yes      | Unique identifier for accessing the value |
| `type`        | `string` | Yes      | Must be `"select"`                        |
| `label`       | `string` | No       | Display label for the select              |
| `description` | `string` | No       | Helper text displayed below the select    |

### Value

You can set default values and bind to measurements or unit properties.

| Property        | Type     | Required | Description                                                                                                                                |
| --------------- | -------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| `default_value` | `string` | No       | Default selected option value                                                                                                              |
| `bind`          | `string` | No       | Bind to unit property or measurement. See [Bind to Measurements](/docs/frameworks/tofupilot/operator-ui#bind-to-measurements) for details. |

### Options

You can define the list of choices available in the select.

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

| Property   | Type      | Required | Description                                                      |
| ---------- | --------- | -------- | ---------------------------------------------------------------- |
| `required` | `boolean` | No       | If true, operator must select an option (shows "Required" badge) |

TofuPilot validates operator input before submission, preventing continuation until all constraints are met. For some workflows, this input validation can create workarounds where operators bypass the UI to proceed.

Instead, you can capture the input as a Measurement using [Binding](/docs/frameworks/tofupilot/operator-ui#bind-to-measurements) and validate it with [Validators](/docs/frameworks/tofupilot/measurements). TofuPilot will record the actual operator input while enforcing pass/fail criteria.

## Examples

### Product Variant Selection

We'll let the operator select from multiple product variants:

```yaml
main:
  - name: Select Variant
    ui:
      components:
        - key: product_variant
          type: select
          label: "Product Variant"
          required: true
          options: # [!code ++]
            - label: "Standard - 8GB RAM" # [!code ++]
              value: "std_8gb" # [!code ++]
            - label: "Standard - 16GB RAM" # [!code ++]
              value: "std_16gb" # [!code ++]
            - label: "Pro - 16GB RAM" # [!code ++]
              value: "pro_16gb" # [!code ++]
            - label: "Pro - 32GB RAM" # [!code ++]
              value: "pro_32gb" # [!code ++]
            - label: "Enterprise - 64GB RAM" # [!code ++]
              value: "ent_64gb" # [!code ++]
```

### Operator Selection

We'll track which operator is performing the test:

```yaml
main:
  - name: Operator Login
    ui:
      components:
        - key: operator_name
          type: select
          label: "Operator"
          description: "Select your name" # [!code ++]
          required: true
          options: # [!code ++]
            - label: "Alice Johnson" # [!code ++]
              value: "alice" # [!code ++]
            - label: "Bob Smith" # [!code ++]
              value: "bob" # [!code ++]
            - label: "Carol Davis" # [!code ++]
              value: "carol" # [!code ++]
            - label: "David Lee" # [!code ++]
              value: "david" # [!code ++]
```
