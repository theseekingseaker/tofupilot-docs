<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/operator-ui/text -->
# Text
URL: /docs/frameworks/tofupilot/operator-ui/text

Display read-only text that updates dynamically during test execution to show status messages and results in TofuPilot.

***

title: Text
description: Display read-only text that updates dynamically during test execution to show status messages and results in TofuPilot.
icon: CaseSensitive
-------------------

import { IsolatedUIPreview } from '@/components/IsolatedUIPreview';
import { Callout } from 'fumadocs-ui/components/callout';

## Preview \[toc]

<IsolatedUIPreview type="text" label="Voltage Reading" value="4.987 V" size="xl" color="lime" font="monospace" containerHeight="200px" />

<div className="sr-only" aria-hidden="false">
  ```
  Operator UI: Text (display only)
    Label: Voltage Reading
    Value: 4.987 V (xl, lime, monospace)
  ```
</div>

You can create a text display with `text`:

```yaml
ui:
  components:
    - key: voltage_reading # [!code ++]
      type: text # [!code ++]
      label: "Voltage Reading" # [!code ++]
      default_value: "4.987 V" # [!code ++]
      size: xl # [!code ++]
      color: lime # [!code ++]
      font: monospace # [!code ++]
```

## Properties

### Display

You can configure how the text appears to the operator.

| Property        | Type     | Required | Description                              |
| --------------- | -------- | -------- | ---------------------------------------- |
| `key`           | `string` | Yes      | Unique identifier for updating the value |
| `type`          | `string` | Yes      | Must be `"text"`                         |
| `label`         | `string` | No       | Display label above the text             |
| `description`   | `string` | No       | Helper text displayed below the label    |
| `default_value` | `string` | No       | Text content to display                  |

### Styling

You can customize the text appearance with size, color, and font options.

| Property | Type     | Required | Description |
| -------- | -------- | -------- | ----------- |
| `size`   | `string` | No       | Text size   |
| `color`  | `string` | No       | Text color  |
| `font`   | `string` | No       | Font family |

#### Sizes

| Size   | Example                                           |
| ------ | ------------------------------------------------- |
| `xs`   | <span className="text-xs">Extra small</span>      |
| `sm`   | <span className="text-sm">Small</span>            |
| `base` | <span className="text-base">Base (default)</span> |
| `lg`   | <span className="text-lg">Large</span>            |
| `xl`   | <span className="text-xl">Extra large</span>      |

#### Colors

| Color     | Example                                                                     | Color    | Example                                                                   | Color   | Example                                                                 |
| --------- | --------------------------------------------------------------------------- | -------- | ------------------------------------------------------------------------- | ------- | ----------------------------------------------------------------------- |
| `default` | <div className="w-4 h-4 rounded-full bg-foreground border border-border" /> | `zinc`   | <div className="w-4 h-4 rounded-full bg-zinc-600 dark:bg-zinc-300" />     | `red`   | <div className="w-4 h-4 rounded-full bg-red-600 dark:bg-red-400" />     |
| `orange`  | <div className="w-4 h-4 rounded-full bg-orange-600 dark:bg-orange-400" />   | `yellow` | <div className="w-4 h-4 rounded-full bg-yellow-600 dark:bg-yellow-400" /> | `green` | <div className="w-4 h-4 rounded-full bg-green-600 dark:bg-green-400" /> |
| `lime`    | <div className="w-4 h-4 rounded-full bg-lime-600 dark:bg-lime-400" />       | `sky`    | <div className="w-4 h-4 rounded-full bg-sky-600 dark:bg-sky-400" />       | `blue`  | <div className="w-4 h-4 rounded-full bg-blue-600 dark:bg-blue-400" />   |
| `violet`  | <div className="w-4 h-4 rounded-full bg-violet-600 dark:bg-violet-400" />   | `purple` | <div className="w-4 h-4 rounded-full bg-purple-600 dark:bg-purple-400" /> | `pink`  | <div className="w-4 h-4 rounded-full bg-pink-600 dark:bg-pink-400" />   |

#### Fonts

| Font        | Example                                                     |
| ----------- | ----------------------------------------------------------- |
| `default`   | <span className="font-sans">Standard sans-serif font</span> |
| `monospace` | <span className="font-mono">Monospace font</span>           |

## Examples

### Voltage Measurement Display

We'll display voltage readings with styling for visibility:

```yaml
name: Power Supply Test

plugs:
  - key: multimeter
    name: Multimeter
    python: plugs.multimeter:Multimeter

main:
  - name: Voltage Test
    python: phases.measure_voltage
    ui:
      components:
        - key: voltage_reading
          type: text
          label: "Measured Voltage"
          description: "Live reading from multimeter"
          default_value: "0.00 V"
          size: xl
          color: lime
          font: monospace
```

```python
import time

def measure_voltage(ui, multimeter):
    for _ in range(10):
        voltage = multimeter.read_voltage()
        ui.voltage_reading = f"{voltage:.2f} V"
        time.sleep(0.5)
```

### Status Updates

We'll update status text as operations progress:

```yaml
name: Burn-In Test

main:
  - name: Long Running Test
    python: phases.show_status
    ui:
      components:
        - key: status
          type: text
          label: "Status"
          default_value: "Initializing..."
          color: blue
```

```python
import time

def show_status(ui):
    operations = [
        "Powering on device",
        "Running self-test",
        "Calibrating sensors",
        "Collecting data"
    ]

    for operation in operations:
        ui.status = operation
        time.sleep(1)

    ui.status = "Complete"
```

### Styled Test Results

We'll use different sizes and colors for visual hierarchy:

```yaml
name: Final Assembly Test

main:
  - name: Test Results
    python: phases.show_results
    ui:
      components:
        - key: title
          type: text
          default_value: "Test Results"
          size: xl
          color: blue

        - key: result_status
          type: text
          default_value: "PASS"
          size: lg
          color: green
          font: monospace

        - key: details
          type: text
          default_value: "All measurements within spec"
          size: sm
          color: zinc
```
