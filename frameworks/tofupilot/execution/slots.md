<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/execution/slots -->
# Slots
URL: /docs/frameworks/tofupilot/execution/slots

Configure multiple test slots to run identical procedures in parallel on separate units, maximizing station throughput in TofuPilot.

***

title: Slots
description: Configure multiple test slots to run identical procedures in parallel on separate units, maximizing station throughput in TofuPilot.
icon: Columns3
--------------

import { Tabs, TabsList, TabsTrigger, TabsContent } from 'fumadocs-ui/components/tabs';
import { IconFileCode } from '@tabler/icons-react';

## Overview \[toc]

You can set `slots` to configure multiple test slots for parallel execution:

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    execution:
      slots: # [!code ++]
        - name: USB0 # [!code ++]
        - name: USB1 # [!code ++]
        - name: USB2 # [!code ++]
    ```
  </TabsContent>
</Tabs>

TofuPilot runs each slot independently, allowing you to test multiple units simultaneously.

Each entry takes a `name` and an optional `key`, the identifier phases see (derived from the name when omitted). For a fixture with many identical positions, give a count instead of a list:

```yaml
execution:
  slots: 80
```

The count expands to keys `slot_01` to `slot_80` and names `Slot 1` to `Slot 80`, zero-padded to the count so the slots sort in order everywhere. The count is capped at 1024, and the engine bounds slots times phases at 10,000 jobs per run. Use the list form for custom names.

## One run per slot \[toc]

Each slot uploads its own run to TofuPilot: its unit, its phases plus the shared `setup` and `teardown` phases at execution scope, its measurements, logs and attachments, and its own outcome. The runs of one start share an `execution_id`, and each carries its `slot_key` and `slot_name`, so the dashboard can group them by start or filter by fixture position. A single-slot procedure uploads exactly as before.

Each slot's unit is identified separately. Use `{slot}` and `{slot_name}` in the unit `default_value` fields to derive serials per slot, see [slot placeholders](/docs/frameworks/tofupilot/unit-under-test#slot-placeholders).

## Failure in one slot \[toc]

A slot stops on its own: a failing phase under `on_first_failure: stop`, `phase.stop()`, an error or a timeout skips the remaining phases of that slot and runs its slot-scoped `teardown` phases while the other slots continue. Every slot gets its own outcome, and the run outcome is the worst of them. Only a failure in a phase shared by all slots (`setup` or `teardown` with `scope: execution`), a plug that fails to initialize, or a stop from the operator stops every slot.

Because a slot's teardown can run while its neighbours are still testing, slot-scoped teardown phases must touch slot-scoped resources only. A shared instrument, a rack power supply, a fixture lock belongs to a teardown phase with `scope: execution`, which runs once after every slot is done.

<Callout type="info" title="Availability">
  Multi-slot execution configuration is not yet available in Studio. Please check our [Discord community](https://discord.gg/tofupilot) for updates on feature availability.
</Callout>
