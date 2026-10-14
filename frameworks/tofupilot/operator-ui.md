<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/operator-ui -->
# Operator UI
URL: /docs/frameworks/tofupilot/operator-ui

Create interactive operator interfaces in TofuPilot with input prompts, visual feedback, and real-time status displays for production testing.

***

title: Operator UI
description: Create interactive operator interfaces in TofuPilot with input prompts, visual feedback, and real-time status displays for production testing.
icon: SquareMousePointer
------------------------

import { Tabs, Tab, TabsList, TabsTrigger, TabsContent } from 'fumadocs-ui/components/tabs';
import { Callout } from 'fumadocs-ui/components/callout';
import { IconFileCode, IconBrandPython } from '@tabler/icons-react';
import { Cards, Card } from 'fumadocs-ui/components/card';
import { TextCursorInput, TextAlignStart, Hash, Settings2, ToggleLeft, CircleDot, SquareCheck, ChevronDown, ChevronsDown, Tag, Image as ImageIcon, BarChart3 } from 'lucide-react';
import { IsolatedUIPreview } from '@/components/IsolatedUIPreview';

## Preview \[toc]

<IsolatedUIPreview
  components={[
  {
    type: "text",
    label: "Measured Voltage",
    value: "3.287 V",
    size: "lg",
    color: "green"
  },
  {
    type: "switch",
    label: "Power Supply Connected",
    description: "Confirm the device is connected to the power supply",
    defaultValue: true
  }
]}
/>

<div className="sr-only" aria-hidden="false">
  ```
  Operator UI Preview (two components in one phase)
    Text:   Measured Voltage = 3.287 V (lg, green)
    Switch: Power Supply Connected = on
            Help: Confirm the device is connected to the power supply
  ```
</div>

## Overview \[toc]

You can create rich and interactive UIs without any external frontend library to install or skills using TofuPilot Operator UI.

```yaml
ui:
  components:
    - key: voltage_reading # [!code ++]
      type: text # [!code ++]
      label: "Measured Voltage" # [!code ++]
      default_value: "0.00 V" # [!code ++]
      size: xl # [!code ++]
      color: lime # [!code ++]
    - key: power_connected # [!code ++]
      type: switch # [!code ++]
      label: "Power Supply Connected" # [!code ++]
      bind: measurements.power_status # [!code ++]
```

TofuPilot provides pre-made components to prompt operator input with bindings and display real-time feedback updated directly from your Python phases.

## Key

You can identify each component with a unique `key` field.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    main:
      - name: Scan Unit
        python: phases.scan_unit
        ui:
          components:
            - key: serial_number # [!code ++]
              type: text_input
              label: "Serial Number"
    ```
  </TabsContent>
</Tabs>

TofuPilot uses the `key` to reference components in Python when retrieving input or updating displays. Studio auto-generates keys when adding components, but you must provide them when writing YAML manually.

## Type

TofuPilot provides pre-made components you can select using the `type` field.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    main:
      - name: Scan Unit
        python: phases.scan_unit
        ui:
          components:
            - key: serial_number
              type: text_input # [!code ++]
              label: "Serial Number"
    ```
  </TabsContent>
</Tabs>

Each component type has its own configuration options documented in dedicated pages.

## Input

### Components

You can use input components to collect operator input during test execution. Their choices and limits are declared in the YAML, and a phase can change them while the screen is open, see [Update Properties](/docs/frameworks/tofupilot/operator-ui#update-properties).

<Cards>
  <Card icon={<TextCursorInput />} href="/docs/frameworks/tofupilot/operator-ui/text-input" title="Text Input">
    Single-line text input field.
  </Card>

  <Card icon={<TextAlignStart />} href="/docs/frameworks/tofupilot/operator-ui/textarea" title="Textarea">
    Multi-line text input area.
  </Card>

  <Card icon={<Hash />} href="/docs/frameworks/tofupilot/operator-ui/number-input" title="Number Input">
    Numeric input with validation.
  </Card>

  <Card icon={<Settings2 />} href="/docs/frameworks/tofupilot/operator-ui/slider" title="Slider">
    Numeric input with min/max range.
  </Card>

  <Card icon={<ToggleLeft />} href="/docs/frameworks/tofupilot/operator-ui/switch" title="Switch">
    Yes/No or True/False toggle.
  </Card>

  <Card icon={<CircleDot />} href="/docs/frameworks/tofupilot/operator-ui/radio" title="Radio">
    Single selection from multiple options or image cards.
  </Card>

  <Card icon={<SquareCheck />} href="/docs/frameworks/tofupilot/operator-ui/checklist" title="Checklist">
    Multiple selections from a list or image cards.
  </Card>

  <Card icon={<ChevronDown />} href="/docs/frameworks/tofupilot/operator-ui/select" title="Select">
    Single selection from dropdown menu.
  </Card>

  <Card icon={<ChevronsDown />} href="/docs/frameworks/tofupilot/operator-ui/multiselect" title="Multiselect">
    Multiple selections from dropdown menu.
  </Card>
</Cards>

### Bind to Measurements

You can bind input component values to measurements, unit properties, or the run's operator attribution using the `bind` field: `measurements.<key>` records the answer as that measurement, `unit.serial_number` / `unit.part_number` route it into the unit, and `run.operated_by` records it as the run's operator, for example from a badge scanner.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    main:
      - name: Scan Unit
        ui:
          components:
            - key: serial_number 
              type: text_input 
              label: "Serial Number" 
              bind: unit.serial_number # [!code ++]
            - key: operator_badge
              type: text_input
              label: "Operator Badge"
              bind: run.operated_by # [!code ++]
      - name: Read Multimeter
        measurements:
          - name: voltage
        ui:
          components:
            - key: meas_input
              type: number_input 
              label: "Voltage" 
              bind: measurements.voltage # [!code ++]
    ```
  </TabsContent>
</Tabs>

<Callout>
  Bindings are applied by the operator UI in the TofuPilot web app. Headless runs driven through the CLI's `--json` agent protocol do not receive the `bind` field; set measurements and unit properties from your Python phases instead.
</Callout>

## Display

### Components

You can use display components to show real-time information and feedback to operators.

<Cards>
  <Card icon={<Tag />} href="/docs/frameworks/tofupilot/operator-ui/text" title="Text">
    Display-only text that updates dynamically.
  </Card>

  <Card icon={<ImageIcon />} href="/docs/frameworks/tofupilot/operator-ui/image" title="Image">
    Display static or dynamic images.
  </Card>

  <Card icon={<BarChart3 />} href="/docs/frameworks/tofupilot/operator-ui/progress" title="Progress">
    Progress indicator bar.
  </Card>
</Cards>

### Require Continue

By default, phases with only display components auto-continue without waiting for operator interaction. You can override this with `requires_input: true` to force a **Continue** button, even without input components.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    main:
      - name: Read Instructions
        ui:
          requires_input: true # [!code ++]
          components:
            - key: instructions
              type: text
              label: Instructions
              default_value: "Please verify the test setup before continuing."
              size: lg
    ```
  </TabsContent>
</Tabs>

This is useful for acknowledgement steps where the operator must read information before proceeding.

### Update Display

You can update display components in real-time by assigning values to the `ui` object.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>

    <TabsTrigger value="show_progress.py">
      <IconBrandPython className="size-4" />

      show\_progress.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    main:
      - name: Long Running Test
        python: phases.show_progress
        ui:
          components:
            - key: progress # [!code ++]
              type: progress # [!code ++]
              label: "Test Progress" # [!code ++]
              default_value: 0 # Initial value [!code ++]
    ```
  </TabsContent>

  <TabsContent value="show_progress.py">
    ```python
    import time

    def show_progress(ui):
        for i in range(0, 101, 10):
            ui.progress = i # [!code ++]
            time.sleep(0.5)
    ```
  </TabsContent>
</Tabs>

### Update Properties

A phase can also change a component's properties, not only its value: the options of a select, the label of a text, the bounds of a number input. The component itself stays what the YAML declared, same key and same type. Call `ui.set(key, ...)` with the properties to change. Several properties in one call reach the screen together, and `None` clears a property.

This is how you fill a choice list from your own systems at run time. Declare the component in `procedure.yaml` without options, fill it from Python, and read the operator's choice in the next phase by naming the first phase as a parameter.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>

    <TabsTrigger value="orders.py">
      <IconBrandPython className="size-4" />

      orders.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    main:
      - key: pick_order
        name: Pick Manufacturing Order
        python: phases.orders:pick_order
        ui:
          components:
            - key: mo
              type: select
              label: "Manufacturing Order"
              required: true
              bind: measurements.mo # The choice is recorded as a measurement [!code ++]
      - key: reserve_order
        name: Reserve Order
        python: phases.orders:reserve_order
        depends_on: [pick_order] # Runs after the operator has chosen [!code ++]
    ```
  </TabsContent>

  <TabsContent value="orders.py">
    ```python
    from mes_client import fetch_open_orders, reserve

    def pick_order(ui):
        orders = fetch_open_orders()  # Your MES, ERP or database
        ui.set("mo", # [!code ++]
            options=[{"value": str(o.id), "label": o.name} for o in orders], # [!code ++]
            placeholder="Choose an order") # [!code ++]

    def reserve_order(pick_order, log):
        mo = pick_order.measurements.mo  # The operator's choice
        reserve(mo)
        log.info(f"Reserved {mo}")
    ```
  </TabsContent>
</Tabs>

Until the options arrive, the operator sees "Waiting for options". A required choice cannot be submitted while the list is empty. When the list changes, a selection that is still offered stays selected and one that is not is cleared. The answer is checked against the list the phase sent, so a value outside it is refused.

What a phase can set depends on the component:

| Property                                                 | Components                                                                       |
| -------------------------------------------------------- | -------------------------------------------------------------------------------- |
| `label`, `description`                                   | Every component                                                                  |
| `placeholder`                                            | Text input, textarea, number input, select, multiselect, radio, checklist, image |
| `options`                                                | Select, multiselect, radio, checklist, switch                                    |
| `prefix`, `suffix`                                       | Text input, number input                                                         |
| `min`, `max`                                             | Number input, slider, progress                                                   |
| `step`                                                   | Number input, slider                                                             |
| `min_length`, `max_length`, `pattern`, `pattern_message` | Text input, textarea                                                             |

The key, the type, `bind`, `required`, the layout and the styling stay in the YAML. Setting a property the component does not have raises an error in the phase before anything reaches the screen. A call the component cannot take as a whole, such as a `min` above its `max` or a `pattern` that does not compile, leaves the screen unchanged and writes an error to the phase log.

An option is a plain string, or a dict with a string `value`, an optional `label` (defaults to the value) and an optional `image`. The same limits as the YAML apply: 200 characters per value or label, 12 options when they carry images.

Stations need CLI 1.7.15 or newer. An older station has no `ui.set` and refuses to load a choice component declared without options. A component filled at run time cannot be answered with `--ui-values`: its list is empty when the prompt opens.

## Timing

When a phase with operator UI runs:

1. UI displays immediately to the operator in an expanded phase row
2. Your Python code runs concurrently and can update component values (`ui.<key> = value`) and properties (`ui.set`) in real time
3. After Python finishes:
   * Phases with input components wait for operator to click **Continue**
   * Phases with only display components continue automatically
