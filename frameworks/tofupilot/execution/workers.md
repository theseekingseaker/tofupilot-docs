<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/execution/workers -->
# Workers
URL: /docs/frameworks/tofupilot/execution/workers

Configure worker threads to run multiple test phases in parallel, improving throughput for independent measurements in TofuPilot.

***

title: Workers
description: Configure worker threads to run multiple test phases in parallel, improving throughput for independent measurements in TofuPilot.
icon: Cpu
---------

import { Tabs, TabsList, TabsTrigger, TabsContent } from 'fumadocs-ui/components/tabs';
import { IconFileCode } from '@tabler/icons-react';

## Overview \[toc]

You can set `workers` to control the maximum number of phases running in parallel:

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
      workers: 16 # [!code ++]
    ```
  </TabsContent>
</Tabs>

TofuPilot runs phases without dependencies (see [Depends On](/docs/frameworks/tofupilot/phases/depends-on)) in parallel using a worker pool. It defaults to 8 workers if not specified.

You can match your CPU core count for CPU-intensive tests. For I/O-heavy tests like instrument communication, you should be able to increase to 2-3x your core count, as workers can overlap while waiting for external devices or user input.
