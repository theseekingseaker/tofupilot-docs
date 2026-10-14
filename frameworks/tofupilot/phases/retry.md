<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/phases/retry -->
# Retry
URL: /docs/frameworks/tofupilot/phases/retry

Configure automatic retry limits and delays between failed phase attempts to handle intermittent failures in TofuPilot test runs.

***

title: Retry
description: Configure automatic retry limits and delays between failed phase attempts to handle intermittent failures in TofuPilot test runs.
icon: RotateCw
--------------

import { Callout } from 'fumadocs-ui/components/callout';
import { Tabs, TabsList, TabsTrigger, TabsContent } from 'fumadocs-ui/components/tabs';
import { IconFileCode } from '@tabler/icons-react';

## Overview \[toc]

You can control how many times TofuPilot retries a phase when it explicitly returns a `Retry` action. By default, TofuPilot allows up to 3 retry attempts.

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
      - name: Network Connect
        python: phases/network
        retry: # [!code ++]
          limit: 5 # [!code ++]
          delay: 1s # [!code ++]
    ```
  </TabsContent>
</Tabs>

## Triggers

TofuPilot only retries phases when:

1. The phase explicitly returns `PhaseResult.RETRY` from Python
2. You configure a next action using `then:` to retry on specific outcomes (e.g., `then: fail: retry` or `then: error: retry`)

The retry does **not** happen automatically on failure or measurement validation errors - you must explicitly trigger it.

## Limit

The `limit` specifies how many retry attempts are allowed after the initial execution. For example, `limit: 3` means up to 3 retries after the first attempt (4 total attempts). The default limit is 3 if not specified.

## Delay

You can add an optional delay between retry attempts using human-readable format: `ms` (milliseconds), `s` (seconds), `m` (minutes), `h` (hours).

## Dashboard Visibility

All retry attempts are preserved and uploaded to the dashboard. Each phase stores a `retry_count` field (0 = first attempt, 1 = first retry, etc.) so you can see exactly how many attempts were needed.

In the run detail view, retried phases display a retry badge and an expandable tab view showing each attempt with its own measurements, outcome, and duration.
