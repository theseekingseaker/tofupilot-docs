<!-- Source: https://www.tofupilot.com/docs/deployments -->
# Deployments
URL: /docs/deployments

Learn how TofuPilot deployments build a procedure version from Git and ship it to one or many factory stations with status, environment, and trigger metadata.

***

title: Deployments
description: "Learn how TofuPilot deployments build a procedure version from Git and ship it to one or many factory stations with status, environment, and trigger metadata."
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------

import { OrderedTypeTable } from '@/components/ordered-type-table';
import { Callout } from 'fumadocs-ui/components/callout';
import { Cards, Card } from 'fumadocs-ui/components/card';
import { GitBranch, Layers, ArrowUpFromLine, Undo2 } from 'lucide-react';

A deployment is the built artifact of a [Procedure](/docs/fundamentals/test-data-model#procedure) at a specific commit. It carries the executable, the resolved runtime, the configuration, and every input the CLI needs to run the procedure on a [Station](/docs/station). The artifact is immutable, so a station running `dep_abc123` always runs the same code.

You can create a deployment in four ways: through a [Git push](#git-push), a [manual trigger](#manual-trigger) from the dashboard, the [CLI](#cli), or the [REST API](#rest-api).

## Deployment methods

TofuPilot exposes the same deployment pipeline through several entry points so you can pick the one that fits your workflow.

### Git push

When you push to a connected [Git repository](/docs/deployments/git), every push to a branch that is not excluded creates a deployment for its latest commit. When auto-push is on, commits on the production branch are built and rolled out to every linked station, and commits on other branches are built as preview deployments that stay off your stations. See [Environments](/docs/deployments/environments#preview) for the exact conditions.

The following providers are supported out of the box:

* **GitHub**: Free, Team, and Enterprise Cloud.
* **GitLab**: Free, Premium, Ultimate, and Self-Managed.
* **Bitbucket**: Data Center / Server. Bitbucket Cloud is not supported yet.

### Manual trigger

From the procedure's **Deployments** page, click **Create Deployment**, then paste a commit SHA or pick a branch. The build worker compiles that exact revision and produces a deployment record.

### CLI

The [CLI](/docs/cli) creates a deployment directly from your local working tree, with or without a connected repo. It uploads the working copy as an artifact and produces a deployment record you can pin to any station.

### REST API

Every deployment action is exposed through the [REST API](/docs/api/rest-api) so you can wire deployments into your own automation:

* `POST /v2/deployments` creates a deployment from a commit.
* `GET /v2/deployments` lists every deployment for a procedure.
* `POST /v2/stations/{id}/deployments/{deployment_id}` pins a deployment to a station.

## Lifecycle

A deployment moves through a small set of statuses from queue to finished build. The table below lists what each status means.

| Status     | Meaning                                                                           |
| ---------- | --------------------------------------------------------------------------------- |
| `pending`  | Queued for build and waiting for a worker.                                        |
| `building` | The build worker is producing the artifact and logs stream live in the dashboard. |
| `ready`    | Built and signed. The deployment is eligible to run on a station.                 |
| `failed`   | The build failed and the deployment cannot be pinned.                             |

Failed deployments are not retried automatically. To try again, re-run from the deployment row.

## Environment

Every deployment is created in one of three environments, and the environment is fixed at creation. The label does not change when the production branch moves, even if the same commit is later promoted. See [Environments](/docs/deployments/environments) for the full lifecycle.

| Environment   | Created by                                                                    |
| ------------- | ----------------------------------------------------------------------------- |
| `production`  | A push to the production branch, or a manual promote of a preview deployment. |
| `preview`     | A push to any non-production branch, or a manual trigger from a commit.       |
| `development` | A local CLI session. The artifact is never uploaded as a long-lived record.   |

## Trigger

Every deployment records how it was created so you can audit the source of a build.

| Trigger  | Created by                                                         |
| -------- | ------------------------------------------------------------------ |
| `auto`   | Fan-out from a push webhook on the linked Git repo.                |
| `manual` | A click on **Create Deployment** in the dashboard, or an API call. |

## Fields

Each deployment exposes the following fields through the API and dashboard.

<OrderedTypeTable
  type={{
  id: { description: 'Deployment UUID. The join key for every API call.', type: 'string' },
  procedure: { description: 'Procedure this deployment belongs to.', type: 'object' },
  commit: { description: 'Commit the artifact was built from (sha, branch, message, author).', type: 'object' },
  environment: { description: '`production`, `preview`, or `development`.', type: 'enum' },
  trigger: { description: '`auto` or `manual`.', type: 'enum' },
  status: { description: '`pending`, `building`, `ready`, or `failed`.', type: 'enum' },
  platform: { description: 'Target platform tag (e.g. `linux_x86_64`, `linux_arm64`, `windows_x86_64`).', type: 'string | null' },
  language: { description: 'Detected language stack such as `python` or `csharp`.', type: 'string | null' },
  runtime_version: { description: 'Resolved runtime version used at build time (e.g. `3.12.13`).', type: 'string | null' },
  started_at: { description: 'Build start time.', type: 'datetime | null' },
  ended_at: { description: 'Build end time.', type: 'datetime | null' },
  artifact_size_bytes: { description: 'Artifact size in bytes.', type: 'integer | null' },
}}
/>

## Fan-out to stations

When auto-push is on, production deployments roll out automatically. Every station bound to the production branch picks up the new deployment between [Runs](/docs/fundamentals/test-data-model#run), and the swap happens during the idle window so in-flight runs finish against the version they started with.

Each station tracks one active deployment per procedure, and a newer deployment moves the active pointer forward. Removing the deployment or unlinking the procedure clears the pointer, after which the station stops accepting runs for that procedure until a new deployment arrives.

If you prefer to stage the rollout instead of fanning out at once, see [Rolling rollout](/docs/deployments/rolling-rollout).

## Auto-push configuration

You configure auto-push from the procedure's **Settings > Repository** page, where you can:

* Toggle **Auto-push** on or off.
* Pick the **production branch** (default `main`).
* Exclude branches you do not want to build.

<Callout type="info">
  Disable auto-push when you want to decide yourself what gets built and rolled out. Pushes are still recorded, but nothing is built. Deploy a commit from the dashboard to build it, then push it to your stations.
</Callout>

## Managing deployments

The **Deployments** tab lists every build with its status, commit, build duration, artifact size, and active stations. From each row, you can take the following actions:

* **Pin to a Station** pushes the build to one or more stations.
* **Promote to production** turns a preview deployment into the active production artifact.
* **Rollback** re-pins every production station to a previous build, as described in [Instant rollback](/docs/deployments/instant-rollback).
* **Inspect** opens the build logs, environment, runtime version, and artifact contents.

## Inspecting a deployment

Open any deployment to see the full context of the build:

* Commit metadata (SHA, message, author, branch).
* Build logs with timestamps.
* Resolved runtime version and platform. Stations provision the matching minor line — see [Python version](/docs/frameworks/tofupilot/phases/python#python-version).
* Artifact contents (procedure YAML, Python packages, supporting files).
* Stations currently running this deployment.
* Runs produced by this deployment.

## Deleting a deployment

You cannot delete a deployment while any station is pinned to it. Move every station to a different deployment first, then delete. Deletion is permanent.

<Cards>
  <Card icon={<GitBranch />} title="Git" href="/docs/deployments/git" description="Connect a repository and configure branch tracking." />

  <Card icon={<Layers />} title="Environments" href="/docs/deployments/environments" description="Production, preview, development." />

  <Card icon={<ArrowUpFromLine />} title="Rolling rollout" href="/docs/deployments/rolling-rollout" description="Promote one station at a time." />

  <Card icon={<Undo2 />} title="Instant rollback" href="/docs/deployments/instant-rollback" description="Revert to a previous build." />
</Cards>
