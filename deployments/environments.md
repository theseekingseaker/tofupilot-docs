<!-- Source: https://www.tofupilot.com/docs/deployments/environments -->
# Environments
URL: /docs/deployments/environments

Learn how TofuPilot tags every deployment with production, preview, or development so you can ship safely from laptop to factory.

***

title: Environments
description: "Learn how TofuPilot tags every deployment with production, preview, or development so you can ship safely from laptop to factory."
------------------------------------------------------------------------------------------------------------------------------------------------

import { Callout } from 'fumadocs-ui/components/callout';
import { Cards, Card } from 'fumadocs-ui/components/card';
import { Box, ArrowUpFromLine, Undo2 } from 'lucide-react';

Every [Deployment](/docs/deployments) carries one of three environment labels, and the label is fixed at build time. The environment determines where the build can run, how long it lives, and who can promote it.

## The three environments

The table below summarizes how each environment is triggered, how long it sticks around, and which stations it can run on.

| Environment     | Triggered by                                                                                                                         | Lifetime       | Runs on                                                                                    |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------ | -------------- | ------------------------------------------------------------------------------------------ |
| **Production**  | Push to the production branch, manual deploy of a production-branch commit from the dashboard, or promotion of a preview deployment. | Permanent.     | Every linked station with auto-push. A manual deploy reaches no station until you push it. |
| **Preview**     | Push to a non-production branch, manual deploy of a commit from the dashboard, or `tofupilot deploy` without `--prod`.               | Permanent.     | No station until you push it manually.                                                     |
| **Development** | Local [CLI](/docs/cli) build.                                                                                                        | Session-bound. | Local CLI session only.                                                                    |

## Production

You create a production deployment in one of two ways:

* A commit lands on the [Procedure](/docs/fundamentals/test-data-model#procedure)'s [production branch](/docs/deployments/git#production-branch). When auto-push is on, the build worker compiles the artifact and rolls it out to every linked [Station](/docs/station). The push must also meet the [build conditions](#preview) listed for previews.
* A Developer, Admin or Owner promotes a preview deployment to production.

Production deployments are permanent. Replaced builds stay in the registry, so you can use [Instant rollback](/docs/deployments/instant-rollback) to pin a station to any previous version.

<Callout type="info">
  Disable auto-push on a procedure when you want to decide yourself what gets built and rolled out. Pushes are still recorded, but nothing is built. Deploy a commit from the dashboard to build it, then push it to your stations.
</Callout>

## Preview

Preview deployments build the same way production deployments do and use the same artifact format. A preview never rolls out to a station on its own, even when auto-push is on.

You can create a preview deployment in three ways:

* **Git push**: a push to a branch other than the production branch builds a preview when the branch is not excluded, auto-push is on, the commit author is a member allowed to deploy, at least one station is linked to the procedure, and production is not rolled back. Otherwise the push is recorded as skipped and nothing is built. A push to an excluded branch leaves no record at all.
* **Dashboard**: deploy a commit from a non-production branch by hand. Use this to build a push that was skipped.
* **CLI**: `tofupilot deploy` without `--prod` builds a preview from your local files.

To test a preview on a station, open the deployment in the dashboard and push it to the station. A station runs one deployment per [Procedure](/docs/fundamentals/test-data-model#procedure), so the preview replaces the version that station was running until the next production rollout or manual push.

<Callout type="warn">
  Stations are not yet split into development and production stations. Pushing a preview to a station on your production line changes the code that line runs.
</Callout>

## Development

Development builds are session-bound and never leave the developer's laptop. [CLI](/docs/cli) builds locally, runs against the [Operator UI](/docs/frameworks/tofupilot/operator-ui), and uploads the resulting [Run](/docs/fundamentals/test-data-model#run), but the artifact itself is not uploaded.

When you want to share work-in-progress, push the branch and TofuPilot builds a preview deployment.

## Environment-specific variables

TofuPilot does not inject environment-specific variables at build time. Configure MES endpoints and calibration data in the procedure YAML, then override per station in the station configuration.

<Cards>
  <Card icon={<Box />} title="Deployments" href="/docs/deployments" description="Record fields and status." />

  <Card icon={<ArrowUpFromLine />} title="Rolling rollout" href="/docs/deployments/rolling-rollout" description="Promote a build station by station." />

  <Card icon={<Undo2 />} title="Instant rollback" href="/docs/deployments/instant-rollback" description="Revert to a previous deployment." />
</Cards>
