<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/operator-ui/text-input -->
# Text Input
URL: /docs/frameworks/tofupilot/operator-ui/text-input

Prompt the operator to enter single-line text during test execution for serial numbers, labels, or identifiers in TofuPilot.

***

title: Text Input
description: Prompt the operator to enter single-line text during test execution for serial numbers, labels, or identifiers in TofuPilot.
icon: TextCursorInput
---------------------

import { IsolatedUIPreview } from '@/components/IsolatedUIPreview';
import { Callout } from 'fumadocs-ui/components/callout';

## Preview \[toc]

<IsolatedUIPreview type="text_input" label="Serial Number" placeholder="SN-001" description="Enter the unit's serial number to start testing" required={true} />

<div className="sr-only" aria-hidden="false">
  ```
  Operator UI: Text Input
    Label: Serial Number (required)
    Placeholder: SN-001
    Help: Enter the unit's serial number to start testing
  ```
</div>

You can create a text input with `text_input`:

```yaml
ui:
  components:
    - key: serial_number # [!code ++]
      type: text_input # [!code ++]
      label: "Serial Number" # [!code ++]
      placeholder: "SN-001" # [!code ++]
      description: "Enter the unit's serial number to start testing" # [!code ++]
      required: true # [!code ++]
```

## Properties

### Display

You can configure how the input field appears to the operator.

| Property      | Type     | Required | Description                               |
| ------------- | -------- | -------- | ----------------------------------------- |
| `key`         | `string` | Yes      | Unique identifier for accessing the value |
| `type`        | `string` | Yes      | Must be `"text_input"`                    |
| `label`       | `string` | No       | Display label for the input               |
| `description` | `string` | No       | Helper text displayed below the input     |
| `placeholder` | `string` | No       | Hint text shown when empty                |

### Value

You can set default values and bind to measurements or unit properties.

| Property        | Type     | Required | Description                                                                                                                                |
| --------------- | -------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| `default_value` | `string` | No       | Default value to pre-fill the input field                                                                                                  |
| `bind`          | `string` | No       | Bind to unit property or measurement. See [Bind to Measurements](/docs/frameworks/tofupilot/operator-ui#bind-to-measurements) for details. |

### Modifiers

You can automatically add prefix or suffix to the operator's input when the value is submitted.

| Property | Type      | Required | Description                                                                             |
| -------- | --------- | -------- | --------------------------------------------------------------------------------------- |
| `prefix` | `string`  | No       | Fixed text locked before the input and prepended to the recorded value (e.g., `"PCB-"`) |
| `suffix` | `string`  | No       | Fixed text locked after the input and appended to the recorded value (e.g., `"-REV"`)   |
| `trim`   | `boolean` | No       | Remove leading and trailing whitespace from input (default: `true`)                     |

The prefix and suffix render as fixed text locked around the input — the operator types only the middle part, and the value recorded through `bind` is `prefix + input + suffix`. An input left empty stays empty; it never records a bare prefix.

### Validation

You can validate input format and length before the value is submitted.

| Property          | Type      | Required | Description                                                                                   |
| ----------------- | --------- | -------- | --------------------------------------------------------------------------------------------- |
| `required`        | `boolean` | No       | If true, operator must provide input (shows "Required" badge)                                 |
| `min_length`      | `number`  | No       | Minimum number of characters required                                                         |
| `max_length`      | `number`  | No       | Maximum number of characters allowed                                                          |
| `pattern`         | `string`  | No       | Regex the whole entry must match (anchors optional)                                           |
| `pattern_message` | `string`  | No       | Error shown when the entry doesn't match `pattern` — include an example the operator can copy |

These constraints validate what the operator types — before any `prefix`/`suffix` is added — so a pattern never needs to repeat the prefix.

On the CLI, Station runs and the web operator UI, a `pattern` must match the **entire** entry, not merely appear somewhere in it: `SN-\d{4}` accepts `SN-0042` and rejects `SCRAP SN-0042`. Writing the anchors is optional there — `[A-Z]{4}` and `^[A-Z]{4}$` are the same rule.

A phase can change `min_length`, `max_length`, `pattern`, `pattern_message`, `prefix` and `suffix` while the screen is open, for example a serial format that depends on the product under test. See [Update Properties](/docs/frameworks/tofupilot/operator-ui#update-properties).

These constraints gate the entry, not the test result: a failing entry is refused at submission, but it never fails the run. Do not rely on them as pass/fail criteria.

Instead, you can capture the input as a Measurement using [Binding](/docs/frameworks/tofupilot/operator-ui#bind-to-measurements) and validate it with [Validators](/docs/frameworks/tofupilot/measurements). TofuPilot will record the actual operator input while enforcing pass/fail criteria.

## Examples

### Length Validation

We'll enforce character count limits:

```yaml
main:
  - name: Enter Part Code
    ui:
      components:
        - key: part_code
          type: text_input
          label: "Part Code"
          min_length: 3 # [!code ++]
          max_length: 10 # [!code ++]
          required: true
```

### Pattern Validation

We'll use regex pattern to enforce specific formats:

```yaml
main:
  - name: Collect Serial Number
    ui:
      components:
        - key: serial_number
          type: text_input
          label: "Serial Number"
          placeholder: "SN-12345678"
          pattern: "^SN-[0-9]{8}$" # [!code ++]
          pattern_message: "SN- followed by 8 digits, like SN-12345678" # [!code ++]
          required: true
```

A pattern that reads as a fixed sequence of characters and character sets (`"^[A-Z0-9-]+$"`, `"^\\d{4}$"`, `"^SN-\\d{4}$"`) explains itself — the operator sees the exact offending character, like *Remove the space (character 3) — allowed: uppercase letters, digits, -* or *Character 6 “X” should be a digit*. A pattern that branches (alternation, groups, lookaheads) can't, so without a `pattern_message` the operator only sees *Doesn't match the required format*.

### Prefix and Suffix

We'll automatically add prefix or suffix to the submitted value:

```yaml
main:
  - name: Component Information
    ui:
      components:
        - key: part_number
          type: text_input
          label: "Component Part Number"
          placeholder: "Enter part ID"
          bind: unit.part_number
          prefix: "PCB-" # [!code ++]
          # Operator enters "1234" → recorded as "PCB-1234"

        - key: firmware_version
          type: text_input
          label: "Firmware Version"
          placeholder: "2.4.1"
          bind: measurements.firmware_version
          suffix: "-prod" # [!code ++]
          # Operator enters "2.4.1" → recorded as "2.4.1-prod"

        - key: test_reference
          type: text_input
          label: "Test Reference"
          placeholder: "reference-id"
          bind: measurements.test_reference
          prefix: "TEST-" # [!code ++]
          suffix: "-REV" # [!code ++]
          # Operator enters "A1" → recorded as "TEST-A1-REV"
```
