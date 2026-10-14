<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/operator-ui/textarea -->
# Textarea
URL: /docs/frameworks/tofupilot/operator-ui/textarea

Prompt the operator to enter multi-line text during test execution for notes, observations, or failure descriptions in TofuPilot.

***

title: Textarea
description: Prompt the operator to enter multi-line text during test execution for notes, observations, or failure descriptions in TofuPilot.
icon: TextAlignStart
--------------------

import { IsolatedUIPreview } from '@/components/IsolatedUIPreview';
import { Callout } from 'fumadocs-ui/components/callout';

## Preview \[toc]

<IsolatedUIPreview type="textarea" label="Test Notes" placeholder="Enter detailed observations..." description="Provide any observations from the test" required={true} />

<div className="sr-only" aria-hidden="false">
  ```
  Operator UI: Textarea (multi-line)
    Label: Test Notes (required)
    Placeholder: Enter detailed observations...
    Help: Provide any observations from the test
  ```
</div>

You can create a textarea with `textarea`:

```yaml
ui:
  components:
    - key: test_notes # [!code ++]
      type: textarea # [!code ++]
      label: "Test Notes" # [!code ++]
      placeholder: "Enter detailed observations..." # [!code ++]
      description: "Provide any observations from the test" # [!code ++]
      required: true # [!code ++]
```

## Properties

### Display

You can configure how the textarea appears to the operator.

| Property      | Type     | Required | Description                               |
| ------------- | -------- | -------- | ----------------------------------------- |
| `key`         | `string` | Yes      | Unique identifier for accessing the value |
| `type`        | `string` | Yes      | Must be `"textarea"`                      |
| `label`       | `string` | No       | Display label for the textarea            |
| `description` | `string` | No       | Helper text displayed below the textarea  |
| `placeholder` | `string` | No       | Hint text shown when empty                |
| `rows`        | `number` | No       | Number of visible text lines              |

### Value

You can set default values and bind to measurements or unit properties.

| Property        | Type     | Required | Description                                                                                                                                |
| --------------- | -------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| `default_value` | `string` | No       | Default value to pre-fill the textarea                                                                                                     |
| `bind`          | `string` | No       | Bind to unit property or measurement. See [Bind to Measurements](/docs/frameworks/tofupilot/operator-ui#bind-to-measurements) for details. |

### Validation

You can validate input length before the value is submitted.

| Property     | Type      | Required | Description                                                   |
| ------------ | --------- | -------- | ------------------------------------------------------------- |
| `required`   | `boolean` | No       | If true, operator must provide input (shows "Required" badge) |
| `min_length` | `number`  | No       | Minimum number of characters required                         |
| `max_length` | `number`  | No       | Maximum number of characters allowed                          |

A phase can change `min_length` and `max_length` while the screen is open. See [Update Properties](/docs/frameworks/tofupilot/operator-ui#update-properties).

TofuPilot validates operator input before submission, preventing continuation until all constraints are met. For some workflows, this input validation can create workarounds where operators bypass the UI to proceed.

Instead, you can capture the input as a Measurement using [Binding](/docs/frameworks/tofupilot/operator-ui#bind-to-measurements) and validate it with [Validators](/docs/frameworks/tofupilot/measurements). TofuPilot will record the actual operator input while enforcing pass/fail criteria.

## Examples

### Length Validation

We'll enforce character count limits:

```yaml
main:
  - name: Collect Observations
    ui:
      components:
        - key: observations
          type: textarea
          label: "Observations"
          placeholder: "Enter detailed observations..."
          min_length: 10 # [!code ++]
          max_length: 500 # [!code ++]
          required: true
```

### Defect Description

We'll collect detailed defect information:

```yaml
main:
  - name: Report Defect
    ui:
      components:
        - key: defect_description
          type: textarea
          label: "Defect Description"
          placeholder: "Describe the defect in detail..." # [!code ++]
          rows: 5 # [!code ++]
          min_length: 20 # [!code ++]
          required: true
```
