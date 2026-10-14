<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/phases/setup-teardown -->
# Setup / Teardown
URL: /docs/frameworks/tofupilot/phases/setup-teardown

Define setup and teardown stages for TofuPilot test phases to handle hardware initialization, calibration, and resource cleanup.

***

title: Setup / Teardown
description: Define setup and teardown stages for TofuPilot test phases to handle hardware initialization, calibration, and resource cleanup.
icon: ChevronsUpDown
--------------------

import { Callout } from 'fumadocs-ui/components/callout';
import { Tabs, TabsList, TabsTrigger, TabsContent } from 'fumadocs-ui/components/tabs';
import { PhaseFlow } from '@/components/phase-flow';
import { IconFileCode } from '@tabler/icons-react';

## Overview \[toc]

You can organize phases into **setup**, **main**, and **teardown** stages. Setup runs first and must complete successfully. Main runs after setup passes. Teardown always runs at the end, even if earlier phases fail.

## Setup

You can add a `setup` section and register phases like in `main`.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    setup: # [!code ++]
      - name: Setup Phase 1 # [!code ++]
      - name: Setup Phase 2 # [!code ++]

    main:
      - name: Main Phase
    ```
  </TabsContent>
</Tabs>

TofuPilot runs main only if all setup phases pass, otherwise stops the test.

<PhaseFlow
  nodes={[
  { id: 'start', type: 'start', position: { x: 340, y: 0 }, data: { label: 'Start' } },
  { id: 'setup1', type: 'phase', position: { x: 100, y: 100 }, data: { label: 'Setup Phase 1', outcome: 'pass' } },
  { id: 'setup2', type: 'phase', position: { x: 400, y: 100 }, data: { label: 'Setup Phase 2', outcome: 'fail' } },
  { id: 'main', type: 'phase', position: { x: 250, y: 200 }, data: { label: 'Main Phase', outcome: 'skip' } },
  { id: 'end', type: 'end', position: { x: 340, y: 300 }, data: { label: 'End' } },
]}
  edges={[
  { id: 'e-start-setup1', source: 'start', target: 'setup1' },
  { id: 'e-start-setup2', source: 'start', target: 'setup2' },
  { id: 'e-setup1-main', source: 'setup1', target: 'main' },
  { id: 'e-setup2-main', source: 'setup2', target: 'main' },
  { id: 'e-main-end', source: 'main', target: 'end' },
]}
  height="420px"
/>

## Teardown

You can add a `teardown` section and register phases like in `main`.

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
      - name: Main Phase

    teardown: # [!code ++]
      - name: Teardown Phase 1 # [!code ++]
      - name: Teardown Phase 2 # [!code ++]
    ```
  </TabsContent>
</Tabs>

TofuPilot always runs teardown phases, even if setup or main phases fail.

<PhaseFlow
  nodes={[
  { id: 'start', type: 'start', position: { x: 340, y: 0 }, data: { label: 'Start' } },
  { id: 'main', type: 'phase', position: { x: 250, y: 100 }, data: { label: 'Main Phase', outcome: 'fail' } },
  { id: 'teardown1', type: 'phase', position: { x: 100, y: 200 }, data: { label: 'Teardown Phase 1', outcome: 'pass' } },
  { id: 'teardown2', type: 'phase', position: { x: 400, y: 200 }, data: { label: 'Teardown Phase 2', outcome: 'pass' } },
  { id: 'end', type: 'end', position: { x: 340, y: 300 }, data: { label: 'End' } },
]}
  edges={[
  { id: 'e-start-main', source: 'start', target: 'main' },
  { id: 'e-main-teardown1', source: 'main', target: 'teardown1' },
  { id: 'e-main-teardown2', source: 'main', target: 'teardown2' },
  { id: 'e-teardown1-end', source: 'teardown1', target: 'end' },
  { id: 'e-teardown2-end', source: 'teardown2', target: 'end' },
]}
  height="420px"
/>

## Full Example

You can use setup, main, and teardown phases in the same procedure:

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    name: Full Procedure

    setup:
      - name: Setup Phase 1
      - name: Setup Phase 2

    main:
      - name: Main Phase

    teardown:
      - name: Teardown Phase 1
      - name: Teardown Phase 2
    ```
  </TabsContent>
</Tabs>

<PhaseFlow
  nodes={[
  { id: 'start', type: 'start', position: { x: 340, y: 0 }, data: { label: 'Start' } },
  { id: 'setup1', type: 'phase', position: { x: 100, y: 100 }, data: { label: 'Setup Phase 1', outcome: 'pass' } },
  { id: 'setup2', type: 'phase', position: { x: 400, y: 100 }, data: { label: 'Setup Phase 2', outcome: 'fail' } },
  { id: 'main', type: 'phase', position: { x: 250, y: 200 }, data: { label: 'Main Phase', outcome: 'skip' } },
  { id: 'teardown1', type: 'phase', position: { x: 100, y: 300 }, data: { label: 'Teardown Phase 1', outcome: 'pass' } },
  { id: 'teardown2', type: 'phase', position: { x: 400, y: 300 }, data: { label: 'Teardown Phase 2', outcome: 'pass' } },
  { id: 'end', type: 'end', position: { x: 340, y: 400 }, data: { label: 'End' } },
]}
  edges={[
  { id: 'e-start-setup1', source: 'start', target: 'setup1' },
  { id: 'e-start-setup2', source: 'start', target: 'setup2' },
  { id: 'e-setup1-main', source: 'setup1', target: 'main' },
  { id: 'e-setup2-main', source: 'setup2', target: 'main' },
  { id: 'e-main-teardown1', source: 'main', target: 'teardown1' },
  { id: 'e-main-teardown2', source: 'main', target: 'teardown2' },
  { id: 'e-teardown1-end', source: 'teardown1', target: 'end' },
  { id: 'e-teardown2-end', source: 'teardown2', target: 'end' },
]}
  height="520px"
/>
