<!-- Source: https://www.tofupilot.com/docs/access/access-roles -->
# Access Roles
URL: /docs/access/access-roles

Learn how the five TofuPilot access roles control what every member can read and write, and how access groups layer on top to scope visibility.

***

title: Access Roles
description: "Learn how the five TofuPilot access roles control what every member can read and write, and how access groups layer on top to scope visibility."
--------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Cards, Card } from 'fumadocs-ui/components/card';
import { User, Users } from 'lucide-react';

Every member of a TofuPilot organization holds exactly one role, and that role applies across the whole organization. Roles set what a member can do; [access groups](/docs/access/groups) layer on top to scope which stations they see.

Changing a member's role requires the Admin or Owner role.

## The five roles

| Role      | What it is for                                                                                                                                                |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Owner     | Everything an Admin can do, plus billing, SSO and SCIM, ownership transfer, and deleting the organization. One per organization.                              |
| Admin     | Manages members, access groups and integrations, and all test data. Cannot change organization settings or billing.                                           |
| Developer | Builds the test setup: procedures, stations, deployments, workflows and test data. Uses the connected integrations but cannot connect them or manage members. |
| Viewer    | Reads runs, units, parts and analytics in the dashboard without changing anything.                                                                            |
| Operator  | Runs tests from the Operator UI at the bench and never sees the dashboard.                                                                                    |

The [permissions](#permissions) tables below list every action role by role.

## Owner

Each organization has exactly one Owner. Owners can do everything an Admin can, plus billing, SSO and SCIM configuration, ownership transfer, and organization deletion.

## Admin

Admins invite and remove members, change roles, create and delete access groups, and manage test data such as [procedures](/docs/fundamentals/test-data-model#procedure), [stations](/docs/station), [deployments](/docs/deployments), and [runs](/docs/fundamentals/test-data-model#run).

Admins cannot update organization settings, manage billing, or configure SSO and SCIM. Those actions are reserved for the Owner.

## Developer

Developers create and update procedures, stations, deployments, [parts](/docs/inventory/parts), [units](/docs/inventory/units), [batches](/docs/inventory/batches), and other test data. They see every access group's stations, so they can work across production lines without restriction.

Developers cannot manage members, access groups, billing, or SSO.

## Viewer

Viewers browse runs, units, parts, and [analytics](/docs/run-analytics) read-only, without being able to create or modify anything.

What a Viewer sees depends on their access group assignment:

* A Viewer with no access group assignments sees every station.
* A Viewer with access group assignments sees their access groups' stations, plus stations with no access group.

Access groups only scope [stations](/docs/station); runs, units, parts, and analytics stay organization-wide for Viewers. See [access groups](/docs/access/groups#what-access-groups-scope).

## Operator

Operators land on `/operator` on sign-in and never see the dashboard. They see their own profile, the stations they belong to (always group-scoped), and basic organization context. They do not see a member list, peer profiles, runs, procedures, API activity, or billing.

Test data is produced through the [station API key](/docs/access/api-keys#station-keys), not the operator's account. When an operator presses **Run** in the [Operator UI](/docs/frameworks/tofupilot/operator-ui), the run is attributed as `operated_by`: the dashboard forwards the operator's email to the CLI, and the CLI stamps it on `runs.create`. Operators without a TofuPilot account can still be attributed: `operated_by` also accepts a free-text name (from a procedure prompt, a `run.operated_by` binding, or Python code), recorded as a declared name without an account link. CLI-only runs and kiosk-mode mounts with no signed-in user and no declared name stay unattributed.

## Permissions

A ✓ means the role can perform the action. An empty cell means it cannot.

### Test data

| Action                                                            | Owner | Admin | Developer | Viewer | Operator |
| ----------------------------------------------------------------- | :---: | :---: | :-------: | :----: | :------: |
| View runs, units, batches, parts and revisions                    |   ✓   |   ✓   |     ✓     |    ✓   |          |
| Create, edit and delete runs, units, batches, parts and revisions |   ✓   |   ✓   |     ✓     |        |          |

Operators produce runs through the [station API key](/docs/access/api-keys#station-keys), not through their own account.

### Procedures and deployments

| Action                                         | Owner | Admin | Developer | Viewer | Operator |
| ---------------------------------------------- | :---: | :---: | :-------: | :----: | :------: |
| View procedures                                |   ✓   |   ✓   |     ✓     |    ✓   |          |
| Create, edit and delete procedures             |   ✓   |   ✓   |     ✓     |        |          |
| View repositories and deployments              |   ✓   |   ✓   |     ✓     |    ✓   |          |
| Link a repository to a procedure               |   ✓   |   ✓   |     ✓     |        |          |
| Deploy, and promote a deployment to production |   ✓   |   ✓   |     ✓     |        |          |

### Stations

| Action                                                  | Owner | Admin | Developer | Viewer | Operator |
| ------------------------------------------------------- | :---: | :---: | :-------: | :----: | :------: |
| View stations                                           |   ✓   |   ✓   |     ✓     |   ✓¹   |    ✓²    |
| Create, edit and delete stations                        |   ✓   |   ✓   |     ✓     |        |          |
| Manage station API keys and link stations to procedures |   ✓   |   ✓   |     ✓     |        |          |

¹ All stations. Once assigned to an access group, only that group's stations and stations with no access group.
² Only their access groups' stations.

### Workflows, analytics and alerts

| Action                                                      | Owner | Admin | Developer | Viewer | Operator |
| ----------------------------------------------------------- | :---: | :---: | :-------: | :----: | :------: |
| View workflows                                              |   ✓   |   ✓   |     ✓     |    ✓   |          |
| Create, edit and delete workflows                           |   ✓   |   ✓   |     ✓     |        |          |
| View insights and reports                                   |   ✓   |   ✓   |     ✓     |    ✓   |          |
| Create, edit and delete insights and reports                |   ✓   |   ✓   |     ✓     |        |          |
| View alerts and alert rules                                 |   ✓   |   ✓   |     ✓     |    ✓   |          |
| Acknowledge alerts, and create, edit and delete alert rules |   ✓   |   ✓   |     ✓     |        |          |
| View API activity                                           |   ✓   |   ✓   |     ✓     |        |          |

### Integrations

| Action                                                                           | Owner | Admin | Developer | Viewer | Operator |
| -------------------------------------------------------------------------------- | :---: | :---: | :-------: | :----: | :------: |
| View connected integrations (GitHub, GitLab, Bitbucket, Odoo, InvenTree, Linear) |   ✓   |   ✓   |     ✓     |    ✓   |          |
| Use a connected integration: link a repository, deploy, build a workflow         |   ✓   |   ✓   |     ✓     |        |          |
| Connect, change or remove an integration                                         |   ✓   |   ✓   |           |        |          |

### Members and organization

| Action                                                 | Owner | Admin | Developer | Viewer | Operator |
| ------------------------------------------------------ | :---: | :---: | :-------: | :----: | :------: |
| View members                                           |   ✓   |   ✓   |     ✓     |    ✓   |    ✓³    |
| Invite and remove members, and change their role       |   ✓   |   ✓   |           |        |          |
| View pending invitations                               |   ✓   |   ✓   |     ✓     |    ✓   |          |
| View access groups                                     |   ✓   |   ✓   |     ✓     |    ✓   |          |
| Create, edit and delete access groups                  |   ✓   |   ✓   |           |        |          |
| View organization settings                             |   ✓   |   ✓   |     ✓     |    ✓   |     ✓    |
| Edit organization settings and delete the organization |   ✓   |       |           |        |          |

³ Only their own profile.

## Roles vs access groups

[Access groups](/docs/access/groups) sit on an orthogonal layer to roles, so the two combine when you decide which stations someone sees.

* Owners, Admins, and Developers see every station.
* Viewers see all stations without access group assignments, and become group-scoped once you assign them.
* Operators are always group-scoped, and see no stations until they are assigned to an access group.

## Changing a role

Admins and Owners can change a role at any time, and the change applies immediately. For the full walkthrough, see [Managing members](/docs/access/managing-members#change-a-role).

<Cards>
  <Card icon={<User />} title="Managing members" href="/docs/access/managing-members" description="Invite members, change roles, and remove members without losing their test data." />

  <Card icon={<Users />} title="Access groups" href="/docs/access/groups" description="Scope which stations suppliers, departments, or production lines can see." />
</Cards>
