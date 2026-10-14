<!-- Source: https://www.tofupilot.com/docs/deployments/instant-rollback -->
# Instant Rollback
URL: /docs/deployments/instant-rollback

Learn how to re-pin every production station to a previous TofuPilot deployment with no rebuild and zero downtime.

***

title: Instant Rollback
description: "Learn how to re-pin every production station to a previous TofuPilot deployment with no rebuild and zero downtime."
---------------------------------------------------------------------------------------------------------------------------------

import { Step, Steps } from 'fumadocs-ui/components/steps';
import { Callout } from 'fumadocs-ui/components/callout';
import { Cards, Card } from 'fumadocs-ui/components/card';
import { ArrowUpFromLine, Box } from 'lucide-react';

Instant rollback re-pins every production [Station](/docs/station) to a previously built [Deployment](/docs/deployments). There is no rebuild: the active pointer flips, and stations pick up the change between [Runs](/docs/fundamentals/test-data-model#run).

## What changes

The table below shows exactly what rollback touches and what it leaves alone.

| Rolls back                                                                                           | Does not                                                          |
| ---------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------- |
| Active deployment on every station linked to this procedure.                                         | Runs already uploaded. They keep their original deployment ID.    |
| The procedure version operators see.                                                                 | Git history.                                                      |
| Auto-push. It pauses until you push another deployment to all stations or resume it from the banner. | Other procedures on the same stations. Rollback is per-procedure. |

<Callout type="warn">
  Rollback does not delete the bad deployment. It stays in the registry and can be re-pinned.
</Callout>

## Roll back

Follow these steps to roll back from the dashboard.

<Steps>
  <Step>
    Open the procedure and go to **Deployments**.
  </Step>

  <Step>
    Open the **⋯** menu on the deployment to return to and select **Instant Rollback**. Eligible targets are production deployments with a ready build. Preview deployments and failed builds are not eligible.
  </Step>

  <Step>
    Review the confirmation dialog. It shows the current production deployment and the rollback target side by side.
  </Step>

  <Step>
    Confirm. Every linked station is re-pinned and picks up the change between runs.
  </Step>
</Steps>

While rolled back, the deployments page shows a banner. Auto-push stays paused until you push another deployment to all stations or click **Resume auto-push** in the banner. Resuming does not repoint stations — they keep the pinned deployment until the next push replaces it.

<Callout>
  API and SDK support for rollback is planned but not available yet. Rollback is dashboard-only for now.
</Callout>

<Cards>
  <Card icon={<ArrowUpFromLine />} title="Rolling rollout" href="/docs/deployments/rolling-rollout" description="Push one station at a time." />

  <Card icon={<Box />} title="Deployments" href="/docs/deployments" description="The deployment record reference." />
</Cards>
