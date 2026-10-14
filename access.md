<!-- Source: https://www.tofupilot.com/docs/access -->
# Access
URL: /docs/access

Learn how to manage who can sign in to your TofuPilot organization, what they can do, and how they authenticate across users, access groups, and stations.

***

title: Access
description: "Learn how to manage who can sign in to your TofuPilot organization, what they can do, and how they authenticate across users, access groups, and stations."
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Cards, Card } from 'fumadocs-ui/components/card';
import { User, UserCheck, Users, LogIn, ArrowLeftRight, Shield, Key, Clock } from 'lucide-react';

A TofuPilot organization contains your members, [stations](/docs/station), and test data. Access control determines who can sign in, what they can do, and how they authenticate:

* [Roles](/docs/access/access-roles): Five built-in roles, from Owner down to Operator, set each member's permissions.
* [Access groups](/docs/access/groups): Restrict which stations a member sees, so a supplier or production line only sees its own benches.
* Authentication: [SSO](/docs/access/sso), [SCIM](/docs/access/scim), and [two-factor authentication](/docs/access/two-factor-authentication) connect members to your identity provider, while [API keys](/docs/access/api-keys) and [setup tokens](/docs/access/setup-tokens) authenticate scripts and stations.

<Cards>
  <Card icon={<UserCheck />} title="Access roles" href="/docs/access/access-roles" description="Choose one of five roles, from Owner to Operator, to set what a member can do." />

  <Card icon={<Users />} title="Access groups" href="/docs/access/groups" description="Scope which stations suppliers, departments, or production lines can see." />

  <Card icon={<User />} title="Managing members" href="/docs/access/managing-members" description="Invite members, change roles, and remove members without losing their test data." />

  <Card icon={<LogIn />} title="Single sign-on" href="/docs/access/sso" description="Authenticate members with SAML 2.0 or OIDC through your identity provider." />

  <Card icon={<ArrowLeftRight />} title="SCIM provisioning" href="/docs/access/scim" description="Provision, update, and deactivate members automatically from your directory." />

  <Card icon={<Shield />} title="Two-factor authentication" href="/docs/access/two-factor-authentication" description="Require a passkey or authenticator app on every sign-in." />

  <Card icon={<Key />} title="API keys" href="/docs/access/api-keys" description="Authenticate scripts, stations, and CI with scoped, revocable keys." />

  <Card icon={<Clock />} title="Setup tokens" href="/docs/access/setup-tokens" description="Register a station with a one-hour, single-use CLI credential." />
</Cards>
