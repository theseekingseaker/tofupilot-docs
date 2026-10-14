<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/phases/depends-on -->
# Depends On
URL: /docs/frameworks/tofupilot/phases/depends-on

Define dependencies between test phases to control execution order and skip downstream phases when prerequisites fail in TofuPilot.

***

title: Depends On
description: Define dependencies between test phases to control execution order and skip downstream phases when prerequisites fail in TofuPilot.
icon: CornerLeftUp
------------------

import { Tabs, TabsList, TabsTrigger, TabsContent } from 'fumadocs-ui/components/tabs';
import { Callout } from 'fumadocs-ui/components/callout';
import { PhaseFlow } from '@/components/phase-flow';
import { IconFileCode } from '@tabler/icons-react';

## Overview \[toc]

You can control execution order between phases using the `depends_on` parameter.

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
      - name: Power On
        key: power_on
        python: phases.power_on

      - name: Test WiFi
        key: test_wifi
        python: phases.test_wifi
        depends_on: [power_on] # [!code ++]

      - name: Test Bluetooth
        key: test_bluetooth
        python: phases.test_bluetooth
        depends_on: [power_on] # [!code ++]

      - name: Final Check
        key: final_check
        python: phases.final_check
        depends_on: [test_wifi, test_bluetooth] # [!code ++]
    ```
  </TabsContent>
</Tabs>

TofuPilot waits for all dependencies to complete before executing a phase. Dependencies reference phase keys, not phase names.

<PhaseFlow
  nodes={[
  { id: 'start', type: 'start', position: { x: 340, y: 0 }, data: { label: 'Start' } },
  { id: 'power_on', type: 'phase', position: { x: 250, y: 120 }, data: { label: 'Power On', outcome: 'pass' } },
  { id: 'test_wifi', type: 'phase', position: { x: 100, y: 240 }, data: { label: 'Test WiFi', outcome: 'pass' } },
  { id: 'test_bluetooth', type: 'phase', position: { x: 400, y: 240 }, data: { label: 'Test Bluetooth', outcome: 'pass' } },
  { id: 'final_check', type: 'phase', position: { x: 250, y: 360 }, data: { label: 'Final Check', outcome: 'pass' } },
  { id: 'end', type: 'end', position: { x: 340, y: 480 }, data: { label: 'End' } },
]}
  edges={[
  { id: 'e-start-power', source: 'start', target: 'power_on' },
  { id: 'e-power-wifi', source: 'power_on', target: 'test_wifi' },
  { id: 'e-power-bluetooth', source: 'power_on', target: 'test_bluetooth' },
  { id: 'e-wifi-final', source: 'test_wifi', target: 'final_check' },
  { id: 'e-bluetooth-final', source: 'test_bluetooth', target: 'final_check' },
  { id: 'e-final-end', source: 'final_check', target: 'end' },
]}
  height="600px"
/>

## Validation

TofuPilot validates dependencies to prevent execution errors:

* **Circular dependencies**: Detects and prevents cycles (e.g., A waits for B, B waits for A)
* **Orphaned dependencies**: Errors if a phase depends on a non-existent phase key
* **Maximum dependencies**: Each phase can have up to 100 dependencies
