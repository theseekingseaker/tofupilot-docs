<!-- Source: https://www.tofupilot.com/docs/access/managing-members -->
# Managing Members
URL: /docs/access/managing-members

Learn how to invite users, change roles, and remove members from your TofuPilot organization while keeping their test data traceable.

***

title: Managing Members
description: "Learn how to invite users, change roles, and remove members from your TofuPilot organization while keeping their test data traceable."
----------------------------------------------------------------------------------------------------------------------------------------------------

import { Step, Steps } from 'fumadocs-ui/components/steps';
import { Cards, Card } from 'fumadocs-ui/components/card';
import { UserCheck, Users } from 'lucide-react';

Members of a TofuPilot organization are managed from **Settings > Members**. Each member holds one [access role](/docs/access/access-roles) and can belong to any number of [access groups](/docs/access/groups).

Inviting members, changing roles, and removing members requires the Admin or Owner role.

## Invite a member

<Steps>
  <Step>
    Open **Settings > Members**.
  </Step>

  <Step>
    Enter the email, pick a role, and optionally select access groups.
  </Step>
</Steps>

The invitee appears under **Pending** marked `Invited` and receives an email with a sign-up link that expires after seven days. If the invitee already has a TofuPilot account in another organization, the invitation links the existing account on accept.

## Accept an invitation

Click the link in the invitation email to join. If you already have a TofuPilot account, the link adds you to the organization on sign-in. Otherwise you complete sign-up, and the membership activates once your email is verified.

## Change a role

<Steps>
  <Step>
    Find the member row.
  </Step>

  <Step>
    Click the actions menu and select **Change role**.
  </Step>

  <Step>
    Pick the new role and confirm.
  </Step>
</Steps>

The role applies immediately, with no sign-out required. You cannot change your own role; another Admin or Owner has to do it.

## Manage access group assignments

[Access groups](/docs/access/groups) scope which [stations](/docs/station) a member can see, and a member can belong to any number of access groups. To change a member's assignments, open the actions menu on their row and select **Manage access groups**. For the visibility rules and the full walkthrough, see [access groups](/docs/access/groups).

## Remove a member

Removing a member revokes their access to your organization without deleting data: runs, [procedures](/docs/fundamentals/test-data-model#procedure), and [units](/docs/inventory/units) they created stay accessible and keep their creator attribution. Their API keys and MCP connections for the organization are revoked at the same time.

<Steps>
  <Step>
    Find the member row.
  </Step>

  <Step>
    Click the actions menu and select **Remove from organization**.
  </Step>

  <Step>
    Confirm.
  </Step>
</Steps>

Access is revoked immediately. To restore it, send a new invitation: the user keeps their TofuPilot account and any other organizations they belong to.

Owners cannot be removed directly. Transfer ownership first under **Settings > Organization**.

<Cards>
  <Card icon={<UserCheck />} title="Access roles" href="/docs/access/access-roles" description="Choose one of five roles, from Owner to Operator, to set what a member can do." />

  <Card icon={<Users />} title="Access groups" href="/docs/access/groups" description="Scope which stations suppliers, departments, or production lines can see." />
</Cards>
