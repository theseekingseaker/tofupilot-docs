<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/execution -->
# Execution
URL: /docs/frameworks/tofupilot/execution

Configure how TofuPilot executes your test procedures, including phase order, failure handling, and parallel execution strategies.

***

title: Execution
description: Configure how TofuPilot executes your test procedures, including phase order, failure handling, and parallel execution strategies.
icon: Settings
--------------

import { Cards, Card } from 'fumadocs-ui/components/card';
import { IconColumns3, IconCpu, IconArrowRight, IconCircleX } from '@tabler/icons-react';
import { InlineOutcomeBadge } from '@/components/inline-outcome-badge';
import { Callout } from "fumadocs-ui/components/callout";

## Overview \[toc]

You can configure test execution behavior using the `execution` section in your procedure file.

## Options

TofuPilot provides options to control test execution:

<Cards>
  <Card icon={<IconCircleX />} title="On Fail" description="Control behavior when phases fail" href="/docs/frameworks/tofupilot/execution/callbacks" />

  <Card icon={<IconCpu />} title="Workers" description="Control worker thread count" href="/docs/frameworks/tofupilot/execution/workers" />

  <Card icon={<IconColumns3 />} title="Slots" description="Configure parallel test slots" href="/docs/frameworks/tofupilot/execution/slots" />

  <Card icon={<IconArrowRight />} title="Strategy" description="Choose phase_first or slot_first" href="/docs/frameworks/tofupilot/execution/strategy" />
</Cards>

## Run Outcome

TofuPilot determines the overall run outcome after all phases complete:

| Outcome                                  | Condition                                                         |
| ---------------------------------------- | ----------------------------------------------------------------- |
| <InlineOutcomeBadge outcome="error" />   | Any phase threw unhandled exception                               |
| <InlineOutcomeBadge outcome="aborted" /> | Run was stopped by the operator, or a phase called `phase.stop()` |
| <InlineOutcomeBadge outcome="timeout" /> | Any phase exceeded timeout limit                                  |
| <InlineOutcomeBadge outcome="fail" />    | Any phase failed                                                  |
| <InlineOutcomeBadge outcome="pass" />    | All phases passed                                                 |

TofuPilot evaluates each condition in order and assigns the first matching outcome.

The CLI's exit code follows the outcome: 0 for a pass, 1 for a fail, an error or a timeout, 130 for a run stopped by the operator, and 3 for a run the procedure stopped itself with `phase.stop()`.

A failure stops the scope it happened in. A phase of a slot failing under `on_first_failure: stop` skips the remaining phases of that slot and runs its teardown; the other slots continue. A failing setup or teardown phase shared by all slots stops every slot. Each slot gets its own outcome from its own phases plus the shared ones, and the run outcome is the worst of them.

## Run Again

After a run completes, the execution bar shows the option to quickly re-execute.

Starts a new run with the same unit identification — serial number, part number, revision number, batch number, and sub-units are all reused from the previous run.

The identify unit row will use the same values as finished run. All other behavior is identical to a normal run: phases reset, outcomes are cleared. In multi-slot setups, each slot's last unit is independently reused.

<Callout type="info" title="Auto-identify">
  If the procedure has [`auto_identify: true`](/docs/frameworks/tofupilot/unit-under-test#auto-identify), the Run Again button is
  disabled. Unit identification is already automatic via YAML defaults — use Run
  instead.
</Callout>
