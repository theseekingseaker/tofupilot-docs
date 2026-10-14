<!-- Source: https://www.tofupilot.com/docs/deployments/rolling-rollout -->
# Rolling Rollout
URL: /docs/deployments/rolling-rollout

Learn how to promote a new TofuPilot deployment one station at a time so you can validate the build on a canary before going wide.

***

title: Rolling Rollout
description: "Learn how to promote a new TofuPilot deployment one station at a time so you can validate the build on a canary before going wide."
preview: true
-------------

import { Step, Steps } from 'fumadocs-ui/components/steps';
import { Callout } from 'fumadocs-ui/components/callout';
import { Cards, Card } from 'fumadocs-ui/components/card';
import { Box, Undo2, ChartColumnStacked } from 'lucide-react';

A rolling rollout splits a [Deployment](/docs/deployments) rollout into stages. Each stage pins the new build to a fraction of the [Procedure](/docs/fundamentals/test-data-model#procedure)'s production [Stations](/docs/station), and the remaining stations keep the previous deployment until you advance.

## How it works

You define two or more stages. Each stage targets a fraction of stations and assigns the new deployment, and you advance through them manually. The final stage is always 100%.

Runs on canary and base stations are tagged with their deployment ID, so [Analytics](/docs/run-analytics) splits metrics by deployment.

| Stage       | Typical fraction                 |
| ----------- | -------------------------------- |
| Canary      | 1 station, or \~10% of the fleet |
| Progressive | 25%–50%                          |
| Full        | 100%                             |

## Start a rollout

Follow these steps to launch a staged rollout from the dashboard.

<Steps>
  <Step>
    Push a commit to the production branch, or promote a [preview Deployment](/docs/deployments/environments#preview) to production.
  </Step>

  <Step>
    On the procedure's **Deployments** page, click the new deployment and select **Start rolling rollout**.
  </Step>

  <Step>
    Define the stages.
  </Step>

  <Step>
    Confirm. TofuPilot pins the new deployment to the canary stations, and every other production station keeps the previous deployment.
  </Step>
</Steps>

## Watch the canary

While a rollout is active, the **Deployments** page shows live signals you can use to decide whether to advance:

* The current stage and the fraction of stations on the new build.
* First-pass [yield](/docs/run-analytics) for canary vs base.
* Average [Run](/docs/fundamentals/test-data-model#run) duration for both.
* Error and timeout rates for both.

The same metrics are available on Analytics, filtered by procedure and split by deployment ID.

## Advance

When the canary looks healthy, move to the next stage.

<Steps>
  <Step>
    On the active rollout banner, click **Advance**.
  </Step>

  <Step>
    The next stage activates and more stations pick up the new deployment between runs.
  </Step>

  <Step>
    The final stage completes the rollout. The deployment becomes active on every station, and the previous deployment becomes the rollback target.
  </Step>
</Steps>

## Abort

When the canary looks bad, abort the rollout to revert canary stations.

<Steps>
  <Step>
    On the active rollout banner, click **Abort**.
  </Step>

  <Step>
    Confirm. Canary stations revert to the previous deployment on the next idle window between runs.
  </Step>
</Steps>

The aborted deployment stays in the registry, so you can inspect its logs, push a fix, and start a new rollout.

<Cards>
  <Card icon={<Box />} title="Deployments" href="/docs/deployments" description="The deployment record reference." />

  <Card icon={<Undo2 />} title="Instant rollback" href="/docs/deployments/instant-rollback" description="Revert after full rollout." />

  <Card icon={<ChartColumnStacked />} title="Analytics" href="/docs/run-analytics" description="Yield, duration, and error metrics by deployment." />
</Cards>
