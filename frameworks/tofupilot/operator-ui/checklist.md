<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/operator-ui/checklist -->
# Checklist
URL: /docs/frameworks/tofupilot/operator-ui/checklist

Prompt the operator to check off multiple items from a list during test execution for step-by-step verification in TofuPilot.

***

title: Checklist
description: Prompt the operator to check off multiple items from a list during test execution for step-by-step verification in TofuPilot.
icon: SquareCheck
-----------------

import { IsolatedUIPreview } from '@/components/IsolatedUIPreview';
import { Callout } from 'fumadocs-ui/components/callout';

## Preview \[toc]

<IsolatedUIPreview
  type="checklist"
  label="Visual Inspection Checks"
  description="Select all checks that passed"
  required={true}
  options={[
  { label: "No scratches", value: "no_scratches" },
  { label: "No dents", value: "no_dents" }
]}
/>

<div className="sr-only" aria-hidden="false">
  ```
  Operator UI: Checklist (multi-choice)
    Label: Visual Inspection Checks (required)
    Options: No scratches | No dents
    Help: Select all checks that passed
  ```
</div>

You can create a checklist with `checklist`:

```yaml
ui:
  components:
    - key: visual_checks # [!code ++]
      type: checklist # [!code ++]
      label: "Visual Inspection Checks" # [!code ++]
      description: "Select all checks that passed" # [!code ++]
      required: true # [!code ++]
      options: # [!code ++]
        - label: "No scratches" # [!code ++]
          value: "no_scratches" # [!code ++]
        - label: "No dents" # [!code ++]
          value: "no_dents" # [!code ++]
```

## Properties

### Display

You can configure how the checklist appears to the operator.

| Property      | Type     | Required | Description                                                                                                           |
| ------------- | -------- | -------- | --------------------------------------------------------------------------------------------------------------------- |
| `key`         | `string` | Yes      | Unique identifier for accessing the value                                                                             |
| `type`        | `string` | Yes      | Must be `"checklist"`                                                                                                 |
| `label`       | `string` | No       | Display label for the checklist                                                                                       |
| `description` | `string` | No       | Helper text displayed below the checklist                                                                             |
| `placeholder` | `string` | No       | Image grid only: text shown while images are loading or missing                                                       |
| `columns`     | `number` | No       | Image grid only: number of grid columns. Auto: 2 (≤4 options), 3 (5-9), 4 (10+)                                       |
| `width`       | `string` | No       | Image grid only: grid container width (e.g., `"80%"`, `"100%"`)                                                       |
| `aspect`      | `string` | No       | Image grid only: aspect ratio per card: `"16/9"`, `"4/3"`, `"3/4"`, `"2/3"`, `"9/16"`, `"square"` (default), `"auto"` |
| `fit`         | `string` | No       | Image grid only: how the image fills each card: `"contain"` (default), `"cover"`, `"fill"`                            |

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

| Property | Type     | Description                                                           |
| -------- | -------- | --------------------------------------------------------------------- |
| `label`  | `string` | Text displayed to the operator                                        |
| `value`  | `string` | Value submitted when selected                                         |
| `image`  | `string` | Relative path to an image file, resolved from the procedure directory |

When any option has an `image`, the checklist renders as a grid of image cards instead of checkboxes (max 12 options). The operator can select multiple cards; selected cards are highlighted with a border.

```yaml
ui:
  components:
    - key: detected_defects
      type: checklist
      label: "Select all defects found"
      required: false
      options:
        - label: "Scratch"
          value: "scratch"
          image: ./images/defects/scratch.png # [!code ++]
        - label: "Dent"
          value: "dent"
          image: ./images/defects/dent.png # [!code ++]
        - label: "No defect"
          value: "none"
          image: ./images/defects/ok.png # [!code ++]
```

### Validation

You can validate selection before the value is submitted.

| Property   | Type      | Required | Description                                                                |
| ---------- | --------- | -------- | -------------------------------------------------------------------------- |
| `required` | `boolean` | No       | If true, operator must select at least one option (shows "Required" badge) |

TofuPilot validates operator input before submission, preventing continuation until all constraints are met. For some workflows, this input validation can create workarounds where operators bypass the UI to proceed.

Instead, you can capture the input as a Measurement using [Binding](/docs/frameworks/tofupilot/operator-ui#bind-to-measurements) and validate it with [Validators](/docs/frameworks/tofupilot/measurements). TofuPilot will record the actual operator input while enforcing pass/fail criteria.

## Examples

### Multi-Step Verification

We'll require operators to verify multiple conditions:

```yaml
main:
  - name: Safety Checks
    ui:
      components:
        - key: safety_verification
          type: checklist
          label: "Safety Verification"
          description: "Confirm all safety checks" # [!code ++]
          required: true
          options: # [!code ++]
            - label: "Power disconnected" # [!code ++]
              value: "power_off" # [!code ++]
            - label: "Grounding verified" # [!code ++]
              value: "grounded" # [!code ++]
            - label: "Protective gear worn" # [!code ++]
              value: "gear_on" # [!code ++]
            - label: "Area clear of hazards" # [!code ++]
              value: "area_clear" # [!code ++]
```

### Defect Types

We'll collect multiple defect types if found:

```yaml
main:
  - name: Defect Detection
    ui:
      components:
        - key: defect_types
          type: checklist
          label: "Select All Defect Types Found"
          required: false
          options: # [!code ++]
            - label: "Cosmetic Damage" # [!code ++]
              value: "cosmetic" # [!code ++]
            - label: "Electrical Issue" # [!code ++]
              value: "electrical" # [!code ++]
            - label: "Mechanical Issue" # [!code ++]
              value: "mechanical" # [!code ++]
            - label: "Software Issue" # [!code ++]
              value: "software" # [!code ++]
```
