<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/unit-under-test -->
# Unit Under Test (UUT)
URL: /docs/frameworks/tofupilot/unit-under-test

Identify hardware units under test with serial numbers, part numbers, and sub-unit assemblies for full traceability in TofuPilot.

***

title: Unit Under Test (UUT)
description: Identify hardware units under test with serial numbers, part numbers, and sub-unit assemblies for full traceability in TofuPilot.
icon: ScanQrCode
----------------

import { Tabs, Tab, TabsList, TabsTrigger, TabsContent } from 'fumadocs-ui/components/tabs';
import { Callout } from 'fumadocs-ui/components/callout';
import { IconFileCode, IconBrandPython } from '@tabler/icons-react';
import { IsolatedUIPreview } from '@/components/IsolatedUIPreview';

<IsolatedUIPreview
  components={[
  {
    type: "text_input",
    label: "Serial Number",
    size: "lg",
  },
  {
    type: "text_input",
    label: "Part Number",
    size: "lg",
  },
]}
/>

<div className="sr-only" aria-hidden="false">
  ```
  Operator UI: Unit identification form
    Operator fills serial_number and part_number to identify the unit under test
  ```
</div>

You can identify each unit under test with serial numbers, part numbers, revision numbers, and batch numbers, all stored in test reports for full traceability.

## Identify Unit

To ensure the UUT is identified during a run, TofuPilot adds an automatic Identify Unit phase. The operator fills in the unit fields before testing starts: **serial number and part number are required**, while revision number and batch number are optional. Any `default_value` you configure pre-fills the corresponding field.

To skip this interactive phase or lock some fields to their default, see [Auto-Identify](#auto-identify).

## Serial Number

You can set a unique identifier for each individual unit.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>

    <TabsTrigger value="identify_unit.py">
      <IconBrandPython className="size-4" />

      identify\_unit.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    unit:
      serial_number: # [!code ++]
        default_value: "SN00012345" # [!code ++]
    ```
  </TabsContent>

  <TabsContent value="identify_unit.py">
    ```python
    def identify_unit(unit):
        unit.serial_number = "SN00012345" # [!code ++]
    ```
  </TabsContent>
</Tabs>

TofuPilot Framework requires a serial number for every test run.

## Part Number

You can set the unit type or model number.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>

    <TabsTrigger value="set_part.py">
      <IconBrandPython className="size-4" />

      set\_part.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    unit:
      part_number: # [!code ++]
        default_value: "PCB-MAIN-V2" # [!code ++]
    ```
  </TabsContent>

  <TabsContent value="set_part.py">
    ```python
    def set_part(unit):
        unit.part_number = "PCB-MAIN-V2" # [!code ++]
    ```
  </TabsContent>
</Tabs>

TofuPilot Framework requires a part number for every test run.

## Revision Number

You can optionally set the revision of the unit.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>

    <TabsTrigger value="set_revision.py">
      <IconBrandPython className="size-4" />

      set\_revision.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    unit:
      revision_number: # [!code ++]
        default_value: "Rev C" # [!code ++]
    ```
  </TabsContent>

  <TabsContent value="set_revision.py">
    ```python
    def set_revision(unit):
        unit.revision_number = "Rev C" # [!code ++]
    ```
  </TabsContent>
</Tabs>

TofuPilot Framework stores revision numbers in reports and syncs them to your Dashboard.

## Batch Number

You can optionally set the production batch or lot number.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>

    <TabsTrigger value="set_batch.py">
      <IconBrandPython className="size-4" />

      set\_batch.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    unit:
      batch_number: # [!code ++]
        default_value: "BATCH-2024-001" # [!code ++]
    ```
  </TabsContent>

  <TabsContent value="set_batch.py">
    ```python
    def set_batch(unit):
        unit.batch_number = "BATCH-2024-001" # [!code ++]
    ```
  </TabsContent>
</Tabs>

TofuPilot Framework stores batch numbers in reports and syncs them to your Dashboard.

## Auto-Identify

You can skip the interactive Identify Unit phase by setting `auto_identify: true`. TofuPilot uses the `default_value` fields directly, so the run starts immediately without operator input.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    unit:
      auto_identify: true # [!code ++]
      serial_number:
        default_value: "SN00012345"
      part_number:
        default_value: "PCB-MAIN-V2"
    ```
  </TabsContent>
</Tabs>

This requires `serial_number.default_value` and `part_number.default_value` to be set. Optional fields (`revision_number`, `batch_number`, `sub_units`) use their defaults if provided; an optional field without a default is absent from the run. See use case in [Examples](#auto-identified-with-barcode-scanner).

### Locked fields

To keep the form but fix some of its fields, add `locked: true` next to a `default_value`. The operator sees the value dimmed and read-only, and the run stores the default whatever the client sends. When every visible field is locked, the form is skipped like with `auto_identify`.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    unit:
      serial_number:
        pattern: "^SN\\d{8}$"
      part_number:
        default_value: "PCB-MAIN-V2"
        locked: true # [!code ++]
    ```
  </TabsContent>
</Tabs>

A locked field needs a non-empty `default_value` that passes its own `pattern` and length rules, otherwise the procedure fails to load. The option works on every unit field, including sub-unit serial numbers, metadata keys and `operated_by`.

### Slot placeholders

On a multi-slot station every slot identifies its own unit. Any `default_value` in the `unit:` block (and in the root `operated_by:`) can contain `{slot}` and `{slot_name}`, which expand per slot to the slot key and its display name from [`execution.slots`](/docs/frameworks/tofupilot/execution/slots). With `auto_identify: true` this lets a burn-in rack start with no operator input and no driver script:

```yaml
execution:
  slots:
    - key: s01
      name: Nest 1
    - key: s02
      name: Nest 2

unit:
  auto_identify: true
  serial_number:
    default_value: "BURNIN-{slot}" # s01 → BURNIN-s01
  part_number:
    default_value: "PCB-MAIN-V2"
  batch_number:
    default_value: "{slot_name}" # s01 → Nest 1
```

Without `auto_identify`, the expanded value pre-fills the operator prompt of each slot. Single-slot procedures never see the placeholders and keep them as literal text.

## Sub-units

You can track component serial numbers linked to the main [unit under test](/docs/inventory/units#sub-units).

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>

    <TabsTrigger value="test_phase.py">
      <IconBrandPython className="size-4" />

      test\_phase.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    unit:
      sub_units: # [!code ++]
        - label: "Battery" # [!code ++]
          serial_number: # [!code ++]
            placeholder: "Scan battery" # [!code ++]
            pattern: "^BAT-.*" # [!code ++]
        - label: "Motor" # [!code ++]
          serial_number: # [!code ++]
            placeholder: "Scan motor" # [!code ++]
    ```
  </TabsContent>

  <TabsContent value="test_phase.py">
    ```python
    def test_phase(unit):
        # Dict access (case-insensitive)
        battery_sn = unit.sub_units["battery"]
        motor_sn = unit.sub_units["motor"]

        # Attribute access (lowercase)
        battery_sn = unit.sub_units.battery
        motor_sn = unit.sub_units.motor

        # Iterate all sub-units (keys are lowercase)
        for key, serial in unit.sub_units.items():
            print(f"{key}: {serial}")  # "battery: BAT-...", "motor: MOT-..."
    ```
  </TabsContent>
</Tabs>

TofuPilot Framework collects sub-unit serial numbers during the Identify Unit phase and makes them available in Python as a dictionary.

Each sub-unit requires a `label` and optionally a `serial_number` configuration. The `serial_number` field supports the same options as the main unit fields. See [Field Options](#field-options) for the full list.

<Callout>
  The sub-units must already exist as Units in TofuPilot when creating the Run. Ensure each sub-unit has been tested and registered before referencing it in the assembly. If a sub-unit doesn't exist, the report will be saved locally but won't sync to your Dashboard.
</Callout>

## Metadata

You can attach custom key-value metadata to the unit under test, like a secondary serial number, a firmware version, or a calibration flag. Declared fields render as inputs in the Identify Unit form; values can also be set from a Python phase.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>

    <TabsTrigger value="test_phase.py">
      <IconBrandPython className="size-4" />

      test\_phase.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    unit:
      serial_number:
        pattern: "^SN\\d{8}$"
      part_number:
        default_value: "PCB-MAIN-V2"
      metadata: # [!code ++]
        mac_address: # [!code ++]
          description: "MAC address printed on the label" # [!code ++]
          placeholder: "AA:BB:CC:00:00:00" # [!code ++]
        housing_color: {} # [!code ++]
    ```
  </TabsContent>

  <TabsContent value="test_phase.py">
    ```python
    def test_phase(unit, log):
        # Read the operator-entered value
        mac = unit.metadata.get("mac_address")
        log.info(f"Testing {unit.serial_number} with MAC {mac}")

        # Set or override values from Python
        unit.metadata["firmware_version"] = "2.4.1" # [!code ++]
        unit.metadata["calibrated"] = True # [!code ++]
    ```
  </TabsContent>
</Tabs>

TofuPilot stores unit metadata in test reports and upserts it onto the unit in your Dashboard, where you can filter units by any key.

Metadata fields are always optional: a blank input means the key is absent. Fields render in alphabetical order after the built-in unit fields and support the same options as the main unit fields (see [Field Options](#field-options)). With `auto_identify: true`, metadata fields resolve from their `default_value` and fields without a default are skipped.

Values can be strings, numbers, or booleans (strings up to 50000 characters). Keys are limited to 40 characters (letters, digits, and `_` `.` `:` `+` `-`), with at most 50 keys per unit. A Python assignment with an invalid key or value raises an error at the faulty line and fails the phase. Python values override operator input with the same key.

Runs started with "Run again" reuse the unit identity but not the operator-entered metadata: values from the previous run remain stored on the unit, but the new run does not carry them and phases read them as absent.

To store metadata on the test run instead of the unit, see [Run Metadata](/docs/frameworks/tofupilot/procedures#run-metadata).

## Operated By

You can record who executed the run by declaring a root-level `operated_by` key. It is a run property, not a unit field, so it sits outside the `unit:` block. When declared, the Identify Unit form prompts for it alongside the unit fields.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>

    <TabsTrigger value="test_phase.py">
      <IconBrandPython className="size-4" />

      test\_phase.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    unit:
      serial_number:
        pattern: "^SN\\d{8}$"
    operated_by: {} # [!code ++]
    ```
  </TabsContent>

  <TabsContent value="test_phase.py">
    ```python
    def test_phase(run, log):
        # Read the value entered at identification
        log.info(f"Run executed by {run.operated_by}")

        # Set or correct it from Python (e.g. a badge scanner)
        run.operated_by = "OP-1234" # [!code ++]
    ```
  </TabsContent>
</Tabs>

The value accepts an email address or a free-text name. An email matching a member of your organization links the run to that account. Any other value is recorded verbatim as a declared operator name, which is what operators without a TofuPilot account get on station-key installs. Declared names are informative only and are not verified identities.

The field supports the same options as the unit fields (see [Field Options](#field-options)), and an operator UI input can also route its answer here with `bind: run.operated_by` (see [Operator UI](/docs/frameworks/tofupilot/operator-ui#bind-to-measurements)).

## Field Options

Each unit field accepts the same set of options to configure its operator input and validation rules.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    unit:
      serial_number: # [!code ++]
        description: "Scan the barcode on the back panel" # [!code ++]
        min_length: 8 # [!code ++]
        max_length: 20 # [!code ++]
        pattern: "^SN\\d{8}$" # [!code ++]
      part_number: # [!code ++]
        max_length: 50 # [!code ++]
        pattern: "^PCB-[A-Z]+-V\\d+$" # [!code ++]
      revision_number: # [!code ++]
        pattern: "^Rev [A-Z]$" # [!code ++]
      batch_number: # [!code ++]
        pattern: "^BATCH-\\d{4}-\\d{3}$" # [!code ++]
    ```
  </TabsContent>
</Tabs>

Before each test run, TofuPilot Framework trims whitespace, validates length constraints, and checks regex patterns against the operator input. Validation applies to what the operator types only — a `prefix` or `suffix` is added afterwards, so `min_length`, `max_length`, and `pattern` never need to account for it.

On the CLI, Station runs and the web operator UI, a `pattern` must match the **entire** entry, not merely appear somewhere in it: `SN-\d{4}` accepts `SN-0042` and rejects `SCRAP SN-0042`. Writing the anchors is optional there — `\d{8}` and `^\d{8}$` are the same rule.

Available options for each unit field:

| Option            | Type    | Description                                                                                                                                                                         |
| ----------------- | ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `description`     | string  | Helper text shown below the field label to guide the operator                                                                                                                       |
| `default_value`   | string  | Pre-filled value in operator input form. Must pass the field's own `pattern`, `min_length` and `max_length`: the procedure fails to load otherwise, and Studio flags it as an issue |
| `placeholder`     | string  | Placeholder text shown in input field                                                                                                                                               |
| `min_length`      | integer | Minimum character length (after trimming)                                                                                                                                           |
| `max_length`      | integer | Maximum character length (after trimming)                                                                                                                                           |
| `pattern`         | string  | Regex the whole entry must match (anchors optional)                                                                                                                                 |
| `pattern_message` | string  | Error shown when the entry doesn't match `pattern` — include an example the operator can copy                                                                                       |
| `prefix`          | string  | Fixed text locked before the input (e.g., `"SN-"`), stored as part of the value                                                                                                     |
| `suffix`          | string  | Fixed text locked after the input (e.g., `"-EU"`), stored as part of the value                                                                                                      |
| `locked`          | boolean | Fix the field to its `default_value`: shown dimmed and read-only, requires a non-empty `default_value`                                                                              |

## Python

You can set unit information from any phase function by adding `unit` as a parameter.

<Tabs defaultValue="identify_unit.py">
  <TabsList>
    <TabsTrigger value="identify_unit.py">
      <IconBrandPython className="size-4" />

      identify\_unit.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="identify_unit.py">
    ```python
    def identify_unit(unit, device):
        # Read serial number from device registry
        serial = device.read_register(0x1000)
        unit.serial_number = serial # [!code ++]

        # Derive part number from serial prefix
        if serial.startswith("IX"):
            unit.part_number = "IXRouter-V3" # [!code ++]
        elif serial.startswith("SN"):
            unit.part_number = "PCB-MAIN-V2" # [!code ++]
    ```
  </TabsContent>
</Tabs>

TofuPilot Framework passes the `unit` object to your phase function via dependency injection when you add it as a parameter.

## Examples

### Unit with Validation

A procedure with default values, placeholders, and regex patterns on unit fields. The operator fills in the Identify Unit form before testing starts.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>

    <TabsTrigger value="check_voltage.py">
      <IconBrandPython className="size-4" />

      check\_voltage.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    name: Battery Functional Test

    plugs:
      - key: multimeter
        name: Multimeter
        python: plugs.multimeter:Multimeter

    unit:
      serial_number:
        placeholder: "Enter serial number"
        pattern: "^SN\\d{8}$"
      part_number:
        default_value: "PCB-MAIN-V2"
      revision_number:
        default_value: "Rev C"
      batch_number:
        placeholder: "Enter batch number"
        min_length: 5
        max_length: 20

    main:
      - key: check_voltage
        name: Check Voltage
        python: phases.check_voltage
        measurements:
          - key: voltage
            name: Battery Voltage
            unit: V
            validators:
              - operator: ">="
                expected_value: 3.2
              - operator: "<="
                expected_value: 4.2
    ```
  </TabsContent>

  <TabsContent value="check_voltage.py">
    ```python
    def check_voltage(measurements, multimeter):
        measurements.voltage = multimeter.measure_dc_voltage()
    ```
  </TabsContent>
</Tabs>

### Pattern Message

We'll set a `pattern_message` with an example the operator can copy.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    unit:
      serial_number:
        pattern: "^SN\\d{8}$"
        pattern_message: "SN followed by 8 digits, like SN00421337" # [!code ++]
    ```
  </TabsContent>
</Tabs>

A pattern that reads as a fixed sequence of characters and character sets (`"^[A-Z0-9-]+$"`, `"^\\d{4}$"`, `"^SN\\d{8}$"`) explains itself — the operator sees the exact offending character, like *Remove the space (character 3) — allowed: uppercase letters, digits, -* or *Character 6 “X” should be a digit*. A pattern that branches (alternation, groups, lookaheads) can't, so without a `pattern_message` the operator only sees *Doesn't match the required format*.

### Prefix and Suffix

We'll lock a fixed prefix before the input.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    unit:
      serial_number:
        prefix: "SN-" # [!code ++]
        pattern: "^\\d{8}$" # matches the typed part only
        # Operator enters "00421337" → stored as "SN-00421337"
    ```
  </TabsContent>
</Tabs>

The operator types only the middle part and the stored value is `prefix + input + suffix`. Validation rules (`min_length`, `max_length`, `pattern`) apply to what the operator types, so the pattern never needs to repeat the prefix.

### Auto-Identified with Barcode Scanner

A procedure that uses `auto_identify` with a Python phase that reads the serial number from a connected barcode scanner. This is typical for automated test stations where units are scanned programmatically.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>

    <TabsTrigger value="barcode_scanner.py">
      <IconBrandPython className="size-4" />

      barcode\_scanner.py
    </TabsTrigger>

    <TabsTrigger value="scan_unit.py">
      <IconBrandPython className="size-4" />

      scan\_unit.py
    </TabsTrigger>

    <TabsTrigger value="check_voltage.py">
      <IconBrandPython className="size-4" />

      check\_voltage.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    name: Battery Functional Test

    plugs:
      - key: barcode_scanner
        name: Barcode Scanner
        python: plugs.barcode_scanner:BarcodeScanner
      - key: multimeter
        name: Multimeter
        python: plugs.multimeter:Multimeter

    unit:
      auto_identify: true
      serial_number:
        default_value: "PENDING"
      part_number:
        default_value: "PCB-MAIN-V2"

    main:
      - key: scan_unit
        name: Scan Unit
        python: phases.scan_unit

      - key: check_voltage
        name: Check Voltage
        python: phases.check_voltage
        measurements:
          - key: voltage
            name: Battery Voltage
            unit: V
            validators:
              - operator: ">="
                expected_value: 3.2
              - operator: "<="
                expected_value: 4.2
    ```
  </TabsContent>

  <TabsContent value="barcode_scanner.py">
    ```python
    import serial

    class BarcodeScanner:
        def __init__(self):
            self.connection = serial.Serial("/dev/ttyUSB0", baudrate=9600)

        def scan(self):
            return self.connection.readline().decode().strip()
    ```
  </TabsContent>

  <TabsContent value="scan_unit.py">
    ```python
    def scan_unit(unit, barcode_scanner):
        unit.serial_number = barcode_scanner.scan()
    ```
  </TabsContent>

  <TabsContent value="check_voltage.py">
    ```python
    def check_voltage(measurements, multimeter):
        measurements.voltage = multimeter.measure_dc_voltage()
    ```
  </TabsContent>
</Tabs>
