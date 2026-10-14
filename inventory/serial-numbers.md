<!-- Source: https://www.tofupilot.com/docs/inventory/serial-numbers -->
# Serial Numbers
URL: /docs/inventory/serial-numbers

Learn how to auto-extract a part number from every unit's serial with an organization-wide regex in TofuPilot, so run uploads no longer need part_number.

***

title: Serial Numbers
description: "Learn how to auto-extract a part number from every unit's serial with an organization-wide regex in TofuPilot, so run uploads no longer need part\_number."
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Callout } from 'fumadocs-ui/components/callout';

Serial number extraction is a single organization-wide regex that pulls a [Part number](/docs/inventory/parts) out of the serial when a [Unit](/docs/inventory/units) is created. You can upload runs without passing `part_number`, and TofuPilot derives the part from the serial.

When the regex matches, the last capture group becomes the part number. When it does not, the unit is created without a part, and you can set the part number explicitly on the next call.

<Callout type="warn">
  Serial number extraction is being deprecated. New integrations should send `part_number` explicitly on every run upload. Existing extraction rules continue to work, but the dashboard surfaces a deprecation banner.
</Callout>

## How matching works

The regex follows the standard JavaScript flavor, and the last capture group becomes the part number. There is no normalization beyond the regex output.

| Serial number        | Regex              | Extracted part number                   |
| -------------------- | ------------------ | --------------------------------------- |
| `PCB-A-0001`         | `^([A-Z]+-[A-Z])-` | `PCB-A`                                 |
| `PCB-A-0001`         | `^[A-Z]+-([A-Z])-` | `A`                                     |
| `2024W12-PCB-A-0001` | `-([A-Z]+-[A-Z])-` | `PCB-A`                                 |
| `BatchOnly-001`      | `^[A-Z]+-[A-Z]-`   | *(no match, unit created with no part)* |

The regex applies to every unit created in the organization, and there is no per-part override.

## Identity

Access to the regex depends on the role of the member acting on it.

| Role             | Action                                                        |
| ---------------- | ------------------------------------------------------------- |
| Owner, Admin     | Set, change, or clear the organization regex.                 |
| Developer        | Reads the regex through every run upload, but cannot edit it. |
| Viewer, Operator | No access.                                                    |

The regex is organization-scoped, and self-hosted installs store it on the organization row.

## Dashboard

Open **Settings > Serial number** to set or update the pattern. The editor validates the regex live, and an invalid pattern shows an error while Save stays disabled. Leave the field empty to disable extraction, in which case every unit requires an explicit `part_number`.

The footer reminds you that **part numbers will be extracted automatically at Run creation.** Changing the regex affects only new units, and existing units keep the part they were assigned when they were first created.

## Replace extraction with explicit part numbers

Because extraction is deprecated, you should pass `part_number` on every `runs.create` call.

```python title="upload.py"
client.runs.create(
    procedure_id="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    serial_number="PCB-A-0001",
    part_number="PCB-A",
    outcome="PASS",
)
```

Once every integration sends `part_number`, you can clear the regex under **Settings > Serial number**.
