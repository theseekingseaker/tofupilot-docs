<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/execution/strategy -->
# Strategy
URL: /docs/frameworks/tofupilot/execution/strategy

Choose sequential or parallel execution strategies for test phases and slots to optimize throughput in TofuPilot procedures.

***

title: Strategy
description: Choose sequential or parallel execution strategies for test phases and slots to optimize throughput in TofuPilot procedures.
icon: ArrowRight
----------------

import { Tabs, TabsList, TabsTrigger, TabsContent } from 'fumadocs-ui/components/tabs';
import { IconFileCode } from '@tabler/icons-react';

## Overview \[toc]

You can control how phases execute across slots using the `strategy` setting. TofuPilot defaults to `phase_first` if not specified.

## Phase-First

You can set `strategy` to `phase_first`:

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
      strategy: phase_first # [!code ++]
      slots:
        - name: USB0
        - name: USB1
        - name: USB2
    ```
  </TabsContent>
</Tabs>

TofuPilot executes the same phase across all slots before moving to the next phase.

## Slot-First

You can set `strategy` to `slot_first`:

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
      strategy: slot_first # [!code ++]
      slots:
        - name: USB0
        - name: USB1
        - name: USB2
    ```
  </TabsContent>
</Tabs>

TofuPilot completes all phases for one slot before starting the next slot.

<Callout type="info" title="Availability">
  Multi-slot execution strategy is not yet available in Studio. Please check our [Discord community](https://discord.gg/tofupilot) for updates on feature availability.
</Callout>
