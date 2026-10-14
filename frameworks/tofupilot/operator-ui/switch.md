<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/operator-ui/switch -->
# Switch
URL: /docs/frameworks/tofupilot/operator-ui/switch

Prompt the operator with a toggle switch for true/false decisions during test execution for pass/fail checks in TofuPilot.

***

title: Switch
description: Prompt the operator with a toggle switch for true/false decisions during test execution for pass/fail checks in TofuPilot.
icon: ToggleLeft
----------------

import { IsolatedUIPreview } from '@/components/IsolatedUIPreview';
import { Callout } from 'fumadocs-ui/components/callout';

## Preview \[toc]

<IsolatedUIPreview type="switch" label="Unit Powered On" description="Confirm the unit is powered on before testing" required={true} />

<div className="sr-only" aria-hidden="false">
  ```
  Operator UI: Switch (toggle)
    Label: Unit Powered On (required)
    Help: Confirm the unit is powered on before testing
  ```
</div>

You can create a switch toggle with `switch`:

```yaml
ui:
  components:
    - key: unit_powered # [!code ++]
      type: switch # [!code ++]
      label: "Unit Powered On" # [!code ++]
      description: "Confirm the unit is powered on before testing" # [!code ++]
      required: true # [!code ++]
```

## Properties

### Display

You can configure how the toggle appears to the operator.

| Property      | Type     | Required | Description                               |
| ------------- | -------- | -------- | ----------------------------------------- |
| `key`         | `string` | Yes      | Unique identifier for accessing the value |
| `type`        | `string` | Yes      | Must be `"switch"`                        |
| `label`       | `string` | No       | Display label for the toggle              |
| `description` | `string` | No       | Helper text displayed below the toggle    |

### Value

You can set default values and bind to measurements or unit properties.

| Property        | Type      | Required | Description                                                                                                                                |
| --------------- | --------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| `default_value` | `boolean` | No       | Default value to pre-fill the switch. If not set, switch starts in "Unset" state.                                                          |
| `bind`          | `string`  | No       | Bind to unit property or measurement. See [Bind to Measurements](/docs/frameworks/tofupilot/operator-ui#bind-to-measurements) for details. |

### Validation

You can validate input before the value is submitted.

| Property   | Type      | Required | Description                                                   |
| ---------- | --------- | -------- | ------------------------------------------------------------- |
| `required` | `boolean` | No       | If true, operator must provide input (shows "Required" badge) |

TofuPilot validates operator input before submission, preventing continuation until all constraints are met.

## Examples

### Unset State

Switches without a default value start in an "Unset" state:

```yaml
main:
  - name: Optional Setting
    ui:
      components:
        - key: optional_feature
          type: switch
          label: "Optional Feature"
          # No default_value set - starts as "Unset" # [!code ++]
          description: "Enable if needed, or leave unset"
```

### Feature Toggle

We'll use a switch to enable or disable a feature:

```yaml
main:
  - name: Configure Test
    ui:
      components:
        - key: enable_calibration
          type: switch
          label: "Enable Calibration Mode"
          description: "Run test in calibration mode" # [!code ++]
          required: false
```

### Conditional Flow

We'll use a switch to determine the next step:

```yaml
main:
  - name: Review Results
    ui:
      components:
        - key: retest_required
          type: switch
          label: "Retest Required"
          description: "Check if the unit needs to be retested" # [!code ++]
          required: true
```
