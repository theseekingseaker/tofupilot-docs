<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/execution/callbacks -->
# On Fail
URL: /docs/frameworks/tofupilot/execution/callbacks

Define callback functions to control what happens when test phases fail, including custom error handling and recovery logic in TofuPilot.

***

title: On Fail
description: Define callback functions to control what happens when test phases fail, including custom error handling and recovery logic in TofuPilot.
icon: CircleX
-------------

import { Tabs, TabsList, TabsTrigger, TabsContent } from 'fumadocs-ui/components/tabs';
import { IconFileCode } from '@tabler/icons-react';
import { InlineOutcomeBadge } from '@/components/inline-outcome-badge';

## Overview \[toc]

You can set `on_first_failure` to define behavior when a phase reaches <InlineOutcomeBadge outcome="fail" />.

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
      on_first_failure: continue # [!code ++]

    main:
      - name: Calibration
        python: calibrate
      - name: Functional Test
        python: test_functions
    ```
  </TabsContent>
</Tabs>

With `continue`, remaining phases execute even after a failure. The default `stop` behavior cancels any phases that haven't started yet.
