<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/phases/timeout -->
# Timeout
URL: /docs/frameworks/tofupilot/phases/timeout

Set configurable timeouts on TofuPilot test phases to prevent indefinite execution and ensure consistent test cycle times.

***

title: Timeout
description: Set configurable timeouts on TofuPilot test phases to prevent indefinite execution and ensure consistent test cycle times.
icon: Clock
-----------

import { Tabs, TabsList, TabsTrigger, TabsContent } from 'fumadocs-ui/components/tabs';
import { IconFileCode, IconBrandPython, IconTerminal } from '@tabler/icons-react';
import { SquareMousePointer } from 'lucide-react';
import { InlineOutcomeBadge } from '@/components/inline-outcome-badge';

## Overview \[toc]

You can prevent a phase from running indefinitely by setting a `timeout`. When it elapses, the phase ends with the <InlineOutcomeBadge outcome="timeout" /> outcome.

A phase without a `timeout` has no time limit — Python code, plug calls, and operator input all wait as long as they need to.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    name: Endurance Test

    main:
      - name: Run Long Test
        python: phases.tests
        timeout: 60s # [!code highlight]
    ```
  </TabsContent>
</Tabs>

Durations combine `h` (hours), `m` (minutes), `s` (seconds), and `ms` (milliseconds), like `90s`, `5m`, or `1h30m`. A bare number is interpreted as milliseconds. The value must be between 1 millisecond and 24 hours.

The timeout covers everything the phase does: Python code, plug calls, and operator UI prompts all share the same deadline. TofuPilot terminates phases that exceed their timeout and logs a warning at 75% of the timeout duration, regardless of which runtime you use.

<Tabs defaultValue="python">
  <TabsList>
    <TabsTrigger value="python">
      <IconBrandPython className="size-4" />

      Python
    </TabsTrigger>

    <TabsTrigger value="executable">
      <IconTerminal className="size-4" />

      Executable
    </TabsTrigger>

    <TabsTrigger value="operatorui">
      <SquareMousePointer className="size-4" />

      Operator UI
    </TabsTrigger>
  </TabsList>

  <TabsContent value="python">
    The Python worker process is terminated when a phase exceeds its timeout, including while it waits on a plug call.
  </TabsContent>

  <TabsContent value="executable">
    The executable worker process is terminated when a command exceeds its timeout.
  </TabsContent>

  <TabsContent value="operatorui">
    The UI phase is terminated when it exceeds its timeout, even if waiting for operator input. Without a `timeout`, a prompt waits for the operator indefinitely.
  </TabsContent>
</Tabs>
