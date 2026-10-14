<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/phases/enable-disable -->
# Enable / Disable
URL: /docs/frameworks/tofupilot/phases/enable-disable

Enable or disable individual phases in TofuPilot test procedures to skip steps during runs without deleting them from your code.

***

title: Enable / Disable
description: Enable or disable individual phases in TofuPilot test procedures to skip steps during runs without deleting them from your code.
icon: ToggleLeft
----------------

import { Tabs, TabsList, TabsTrigger, TabsContent } from 'fumadocs-ui/components/tabs';
import { IconFileCode } from '@tabler/icons-react';

## Overview \[toc]

You can disable phases to skip execution without deleting them.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    name: Controller Board Test

    main:
      - name: Debug Logging
        key: debug_logging
        python: phases.debug
        enabled: false  # [!code highlight]
    ```
  </TabsContent>
</Tabs>

TofuPilot skips disabled phases without recording any results.
