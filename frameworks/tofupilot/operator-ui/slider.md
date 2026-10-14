<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/operator-ui/slider -->
# Slider
URL: /docs/frameworks/tofupilot/operator-ui/slider

Prompt the operator to set a numeric value using a visual slider control during test execution for calibration in TofuPilot.

***

title: Slider
description: Prompt the operator to set a numeric value using a visual slider control during test execution for calibration in TofuPilot.
icon: Settings2
---------------

import { IsolatedUIPreview } from '@/components/IsolatedUIPreview';
import { Callout } from 'fumadocs-ui/components/callout';

## Preview \[toc]

<IsolatedUIPreview type="slider" label="Volume" min={0} max={100} step={5} description="Adjust the test volume level" required={true} />

<div className="sr-only" aria-hidden="false">
  ```
  Operator UI: Slider
    Label: Volume (required)
    Range: 0 to 100, step 5
    Help: Adjust the test volume level
  ```
</div>

You can create a slider with `slider`:

```yaml
ui:
  components:
    - key: volume # [!code ++]
      type: slider # [!code ++]
      label: "Volume" # [!code ++]
      min: 0 # [!code ++]
      max: 100 # [!code ++]
      step: 5 # [!code ++]
      description: "Adjust the test volume level" # [!code ++]
      required: true # [!code ++]
```

## Properties

### Display

You can configure how the slider appears to the operator.

| Property      | Type     | Required | Description                               |
| ------------- | -------- | -------- | ----------------------------------------- |
| `key`         | `string` | Yes      | Unique identifier for accessing the value |
| `type`        | `string` | Yes      | Must be `"slider"`                        |
| `label`       | `string` | No       | Display label for the slider              |
| `description` | `string` | No       | Helper text displayed below the slider    |

### Value

You can set default values and bind to measurements or unit properties.

| Property        | Type     | Required | Description                                                                                                                                |
| --------------- | -------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| `default_value` | `number` | No       | Default value to pre-fill the slider. If not set, slider starts in "Unset" state.                                                          |
| `bind`          | `string` | No       | Bind to unit property or measurement. See [Bind to Measurements](/docs/frameworks/tofupilot/operator-ui#bind-to-measurements) for details. |

### Range

You must define the slider's valid range.

| Property | Type     | Required | Description                                       |
| -------- | -------- | -------- | ------------------------------------------------- |
| `min`    | `number` | **Yes**  | Minimum value                                     |
| `max`    | `number` | **Yes**  | Maximum value                                     |
| `step`   | `number` | No       | Increment step for slider movement (default: `1`) |

A phase can change `min`, `max` and `step` while the screen is open. A value left outside the new range moves to its nearest bound. See [Update Properties](/docs/frameworks/tofupilot/operator-ui#update-properties).

### Validation

You can validate input range before the value is submitted.

| Property   | Type      | Required | Description                                                   |
| ---------- | --------- | -------- | ------------------------------------------------------------- |
| `required` | `boolean` | No       | If true, operator must provide input (shows "Required" badge) |

TofuPilot validates operator input before submission, preventing continuation until all constraints are met.

## Examples

### Unset State

Sliders without a default value start in an "Unset" state:

```yaml
main:
  - name: Adjust Setting
    ui:
      components:
        - key: parameter
          type: slider
          label: "Optional Parameter"
          min: 0
          max: 100
          # No default_value set - starts as "Unset" # [!code ++]
          description: "Adjust if needed, or leave unset"
```

### Range Configuration

We'll configure the slider with specific min, max, and step values:

```yaml
main:
  - name: Set Voltage
    ui:
      components:
        - key: voltage
          type: slider
          label: "Test Voltage"
          min: 3.0 # [!code ++]
          max: 12.0 # [!code ++]
          step: 0.1 # [!code ++]
          required: true
```
