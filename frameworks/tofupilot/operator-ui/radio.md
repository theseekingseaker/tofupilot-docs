<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/operator-ui/radio -->
# Radio
URL: /docs/frameworks/tofupilot/operator-ui/radio

Prompt the operator to choose one option from a set of mutually exclusive radio buttons during test execution in TofuPilot.

***

title: Radio
description: Prompt the operator to choose one option from a set of mutually exclusive radio buttons during test execution in TofuPilot.
icon: CircleDot
---------------

import { IsolatedUIPreview } from '@/components/IsolatedUIPreview';
import { Callout } from 'fumadocs-ui/components/callout';

## Preview \[toc]

<IsolatedUIPreview
  type="radio"
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
  Operator UI: Radio (single choice)
    Label: Power Supply Voltage (required)
    Options: 3.3V | 5.0V
    Help: Select the test voltage
  ```
</div>

You can create a radio component with `radio`:

```yaml
ui:
  components:
    - key: voltage # [!code ++]
      type: radio # [!code ++]
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

You can configure how the radio component appears to the operator.

| Property      | Type     | Required | Description                                                                                                           |
| ------------- | -------- | -------- | --------------------------------------------------------------------------------------------------------------------- |
| `key`         | `string` | Yes      | Unique identifier for accessing the value                                                                             |
| `type`        | `string` | Yes      | Must be `"radio"`                                                                                                     |
| `label`       | `string` | No       | Display label for the radio component                                                                                 |
| `description` | `string` | No       | Helper text displayed below the radio component                                                                       |
| `placeholder` | `string` | No       | Image grid only: text shown while images are loading or missing                                                       |
| `columns`     | `number` | No       | Image grid only: number of grid columns. Auto: 2 (≤4 options), 3 (5-9), 4 (10+)                                       |
| `width`       | `string` | No       | Image grid only: grid container width (e.g., `"80%"`, `"100%"`)                                                       |
| `aspect`      | `string` | No       | Image grid only: aspect ratio per card: `"16/9"`, `"4/3"`, `"3/4"`, `"2/3"`, `"9/16"`, `"square"` (default), `"auto"` |
| `fit`         | `string` | No       | Image grid only: how the image fills each card: `"contain"` (default), `"cover"`, `"fill"`                            |

### Value

You can set default values and bind to measurements or unit properties.

| Property        | Type     | Required | Description                                                                                                                                |
| --------------- | -------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| `default_value` | `string` | No       | Default selected option value                                                                                                              |
| `bind`          | `string` | No       | Bind to unit property or measurement. See [Bind to Measurements](/docs/frameworks/tofupilot/operator-ui#bind-to-measurements) for details. |

### Options

You can define the list of mutually exclusive choices.

| Property  | Type    | Required | Description                                                                                                                                                               |
| --------- | ------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `options` | `array` | No       | Array of objects with `label` and `value` properties. Leave it out to fill the list at run time with [`ui.set`](/docs/frameworks/tofupilot/operator-ui#update-properties) |

Each option object has:

| Property | Type     | Description                                                           |
| -------- | -------- | --------------------------------------------------------------------- |
| `label`  | `string` | Text displayed to the operator                                        |
| `value`  | `string` | Value submitted when selected                                         |
| `image`  | `string` | Relative path to an image file, resolved from the procedure directory |

When any option has an `image`, the radio renders as a grid of image cards instead of a button list (max 12 options). The operator selects one card; the selected card is highlighted with a border.

```yaml
ui:
  components:
    - key: cable_type
      type: radio
      label: "Select Cable Type"
      required: true
      options:
        - label: "Ribbon"
          value: "ribbon"
          image: ./images/cable-ribbon.jpg # [!code ++]
        - label: "Flat Flex (FFC)"
          value: "ffc"
          image: ./images/cable-ffc.jpg # [!code ++]
        - label: "Wire Harness"
          value: "harness"
          image: ./images/cable-harness.jpg # [!code ++]
```

### Validation

You can validate selection before the value is submitted.

| Property   | Type      | Required | Description                                                      |
| ---------- | --------- | -------- | ---------------------------------------------------------------- |
| `required` | `boolean` | No       | If true, operator must select an option (shows "Required" badge) |

TofuPilot validates operator input before submission, preventing continuation until all constraints are met. For some workflows, this input validation can create workarounds where operators bypass the UI to proceed.

Instead, you can capture the input as a Measurement using [Binding](/docs/frameworks/tofupilot/operator-ui#bind-to-measurements) and validate it with [Validators](/docs/frameworks/tofupilot/measurements). TofuPilot will record the actual operator input while enforcing pass/fail criteria.

## Examples

### Test Mode Selection

We'll let the operator choose between different test modes:

```yaml
main:
  - name: Configure Test
    ui:
      components:
        - key: test_mode
          type: radio
          label: "Test Mode"
          required: true
          options: # [!code ++]
            - label: "Quick Test (30s)" # [!code ++]
              value: "quick" # [!code ++]
            - label: "Standard Test (2min)" # [!code ++]
              value: "standard" # [!code ++]
            - label: "Thorough Test (5min)" # [!code ++]
              value: "thorough" # [!code ++]
```

### Quality Level

We'll collect quality assessment from the operator:

```yaml
main:
  - name: Quality Assessment
    ui:
      components:
        - key: quality_level
          type: radio
          label: "Quality Level"
          description: "Assess the overall quality" # [!code ++]
          required: true
          options: # [!code ++]
            - label: "Excellent" # [!code ++]
              value: "excellent" # [!code ++]
            - label: "Good" # [!code ++]
              value: "good" # [!code ++]
            - label: "Acceptable" # [!code ++]
              value: "acceptable" # [!code ++]
            - label: "Poor" # [!code ++]
              value: "poor" # [!code ++]
```
