<!-- Source: https://www.tofupilot.com/docs/access/groups -->
# Access Groups
URL: /docs/access/groups

Learn how to group TofuPilot stations and members into access groups so you can scope station visibility for suppliers, departments, or production lines.

***

title: Access Groups
description: "Learn how to group TofuPilot stations and members into access groups so you can scope station visibility for suppliers, departments, or production lines."
------------------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Step, Steps } from 'fumadocs-ui/components/steps';
import { Callout } from 'fumadocs-ui/components/callout';
import { Cards, Card } from 'fumadocs-ui/components/card';
import { UserCheck, User } from 'lucide-react';

Access groups scope which [stations](/docs/station) a member sees, so you can give a supplier, department, or production line its own slice of the organization. A station belongs to one access group, and a member can belong to many.

Creating, renaming, and deleting access groups requires the Admin or Owner [role](/docs/access/access-roles). Access groups are available on all plans.

## What access groups scope

Access groups restrict **station visibility**: the station list, station detail pages, and the stations shown in the [Operator UI](/docs/frameworks/tofupilot/operator-ui).

Other test data is not group-scoped: [runs](/docs/fundamentals/test-data-model#run), [units](/docs/inventory/units), [parts](/docs/inventory/parts), [batches](/docs/inventory/batches), and [analytics](/docs/run-analytics) remain organization-wide for every role that can read them. Operators never see this data regardless of grouping, because their role has no read access to it.

## How visibility works

What a member sees depends on both their role and their access group assignments.

| Role                                 | Stations they see                                                  |
| ------------------------------------ | ------------------------------------------------------------------ |
| Owner, Admin, Developer              | All stations.                                                      |
| Viewer (no access groups)            | All stations, read-only.                                           |
| Viewer (assigned to access groups)   | Their access groups' stations, plus stations with no access group. |
| Operator (assigned to access groups) | Only their access groups' stations.                                |
| Operator (no access groups)          | No stations.                                                       |

A station with no access group is visible to every Viewer and to no Operator.

## Create an access group

You create access groups from the same screen you use for members.

<Steps>
  <Step>
    Open **Settings > Members** and scroll to **Access Groups**.
  </Step>

  <Step>
    Click **Create access group** and name it.
  </Step>
</Steps>

The access group appears in the list, empty until you assign stations and members. Names are unique within the organization and can be up to 60 characters. There is no limit on the number of access groups.

## Assign a station to an access group

Each station belongs to one access group, and changes take effect immediately.

<Steps>
  <Step>
    Open **Stations** and select the station.
  </Step>

  <Step>
    Pick an access group from the dropdown in the station settings and save.
  </Step>
</Steps>

The station is now visible to that access group's Viewers and Operators, plus Owners, Admins, and Developers.

## Assign a member to an access group

Access group assignment only changes what Viewers and Operators see, but you can assign any member ahead of a role change.

<Steps>
  <Step>
    Open **Settings > Members** and find the member.
  </Step>

  <Step>
    Click the actions menu and select **Manage access groups**.
  </Step>

  <Step>
    Check and uncheck access groups, then save.
  </Step>
</Steps>

You can also pick access groups directly when [inviting a member](/docs/access/managing-members#invite-a-member).

## Delete an access group

Deleting an access group does not delete its stations or members. Only the scoping link is removed: the group's stations become unassigned.

<Callout type="warn">
  Unassigned stations are visible to every Viewer and to no Operator. Deleting an access group immediately cuts its Operators off from its stations, and exposes those stations to all Viewers. Reassign the stations to another access group first if that is not what you want.
</Callout>

<Steps>
  <Step>
    Open **Settings > Members** and find the access group.
  </Step>

  <Step>
    Click the actions menu, select **Delete**, and confirm.
  </Step>
</Steps>

<Cards>
  <Card icon={<UserCheck />} title="Access roles" href="/docs/access/access-roles" description="How roles interact with access groups." />

  <Card icon={<User />} title="Managing members" href="/docs/access/managing-members" description="Invite members and assign access groups." />
</Cards>
