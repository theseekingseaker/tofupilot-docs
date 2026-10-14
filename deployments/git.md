<!-- Source: https://www.tofupilot.com/docs/deployments/git -->
# Git
URL: /docs/deployments/git

Learn how to connect a GitHub, GitLab, or Bitbucket repository to a TofuPilot procedure so every push produces a versioned deployment.

***

title: Git
description: "Learn how to connect a GitHub, GitLab, or Bitbucket repository to a TofuPilot procedure so every push produces a versioned deployment."
-----------------------------------------------------------------------------------------------------------------------------------------------------

import { Step, Steps } from 'fumadocs-ui/components/steps';
import { Callout } from 'fumadocs-ui/components/callout';
import { Cards, Card } from 'fumadocs-ui/components/card';
import { Box, Layers } from 'lucide-react';

You connect a repo to a [Procedure](/docs/fundamentals/test-data-model#procedure) once. From that point on, every push to a tracked branch produces a [Deployment](/docs/deployments), and pushes to the production branch push to every [Station](/docs/station) bound to that branch.

## Supported providers

TofuPilot integrates natively with the providers below. For everything else, deploy through the [CLI](/docs/cli) or the [REST API](/docs/api/rest-api).

| Provider                                  | Authentication                | Webhook scope    |
| ----------------------------------------- | ----------------------------- | ---------------- |
| **GitHub** (Free, Team, Enterprise Cloud) | GitHub App                    | App-level        |
| **GitLab** (Free, Premium, Ultimate)      | Group or project access token | Group or project |
| **GitLab Self-Managed**                   | Group or project access token | Group or project |
| **Bitbucket Data Center / Server**        | Project HTTP access token     | Repository       |

<Callout>
  Bitbucket support covers **Data Center / Server** (self-hosted) instances only. Bitbucket Cloud (bitbucket.org) is not supported yet.
</Callout>

## Connect a repository

Follow the steps below to wire a repository to a procedure.

<Steps>
  <Step>
    Open the procedure and go to **Settings > Repository**.
  </Step>

  <Step>
    Pick **GitHub**, **GitLab**, or **Bitbucket**. For GitHub, install the TofuPilot GitHub App on the account or organization. For GitLab, paste a group access token (preferred) or a personal access token with `api` and `read_repository` scopes. For Bitbucket, enter your Data Center instance URL and a project-scoped [HTTP access token](https://confluence.atlassian.com/bitbucketserver/http-access-tokens-939515499.html) with **Project admin** permission, then select the project.

    Connecting a provider requires the Admin or Owner role in TofuPilot. Once a provider is connected, Developers can link repositories and deploy. See [access roles](/docs/access/access-roles#permissions).
  </Step>

  <Step>
    Select the repository. TofuPilot registers the webhook automatically.
  </Step>

  <Step>
    Pick the **production branch**. Pushes to this branch produce production deployments, and pushes to every other branch produce preview deployments.
  </Step>

  <Step>
    Save. The next push to any tracked branch triggers a build.
  </Step>
</Steps>

## Production branch

A [production Deployment](/docs/deployments/environments#production) is created each time a commit lands on the production branch. The default is `main`, and you can change it under **Settings > Repository > Production branch**.

Switching the production branch only affects future pushes. Existing deployments keep their original environment label.

## Preview branches

Every non-production branch is a preview branch. Pushes to these branches produce preview deployments with their own ID, and preview deployments do not auto-promote to production.

## Private repositories

You can connect private repositories from every provider:

* **GitHub**: install the TofuPilot GitHub App with access to the repo. Authentication uses short-lived installation tokens.
* **GitLab**: generate a Group Access Token scoped to the group. The token is encrypted at rest.
* **Bitbucket Data Center**: generate a project-scoped HTTP access token with **Project admin** permission (required to register repository webhooks). The token is encrypted at rest.

When a commit arrives, the committer's TofuPilot identity is matched against the commit author's verified email. If no member matches, the deployment still builds, but it is not auto-promoted.

## Forks of public repositories

Pushes to a fork do not deploy automatically. An Admin or Owner authorizes the deployment from the procedure's **Deployments** page, and authorization is per-PR. Once a PR is authorized, subsequent pushes to the same PR build automatically.

## Re-running a failed deployment

When a build fails, open the failed deployment and click **Rebuild**. The deployment re-runs for the same commit against fresh infrastructure.

## Detaching a repository

When you want to remove the integration, follow these steps:

1. Open **Settings > Repository**.
2. Click **Disconnect repository**.

The webhook is removed, while existing deployments are preserved and stay pinned to their stations.

<Cards>
  <Card icon={<Box />} title="Deployments" href="/docs/deployments" description="The deployment record reference." />

  <Card icon={<Layers />} title="Environments" href="/docs/deployments/environments" description="Production, preview, development." />
</Cards>
