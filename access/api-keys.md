<!-- Source: https://www.tofupilot.com/docs/access/api-keys -->
# API Keys
URL: /docs/access/api-keys

Learn how to authenticate scripts, factory stations, and CI runs with TofuPilot API keys, including how user and station keys differ in scope.

***

title: API Keys
description: "Learn how to authenticate scripts, factory stations, and CI runs with TofuPilot API keys, including how user and station keys differ in scope."
-------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Step, Steps } from 'fumadocs-ui/components/steps';
import { Cards, Card } from 'fumadocs-ui/components/card';
import { UserCheck, Computer, Code } from 'lucide-react';

Anything that talks to the API outside the browser uses an API key. User keys authenticate as a user, and [station](/docs/station) keys authenticate the bench. Both are bearer tokens passed in the `Authorization` header. Any member can create keys for their own account.

| Key type    | Belongs to         | Used by                                        |
| ----------- | ------------------ | ---------------------------------------------- |
| User key    | A specific user    | Personal SDK use, ad-hoc scripts, CI jobs      |
| Station key | A specific station | The CLI in station mode and code on that bench |

## User keys

User keys inherit the role and access group scope of their owner, so a Developer's key writes [runs](/docs/fundamentals/test-data-model#run) while a Viewer's key reads only. You control what a key can do by adjusting the owning user's role.

### Create a user key

<Steps>
  <Step>
    Open **Settings > API keys**.
  </Step>

  <Step>
    Click **Create key**, give it a name, and pick an expiry.
  </Step>

  <Step>
    Copy the key and store it in a secrets manager or a `.env` file.
  </Step>
</Steps>

The key is shown once. After you close the dialog, it cannot be retrieved again.

### Use a user key

Send the key as a bearer token:

```bash title="curl"
curl https://www.tofupilot.app/api/v2/runs \
  -H "Authorization: Bearer $TOFUPILOT_API_KEY"
```

Every SDK reads `TOFUPILOT_API_KEY` from the environment, so you do not need to pass the key in code.

```python title="upload_run.py"
from tofupilot.v2 import TofuPilot

client = TofuPilot()  # reads TOFUPILOT_API_KEY
```

### Revoke a user key

<Steps>
  <Step>
    Open **Settings > API keys**.
  </Step>

  <Step>
    Find the key and select **Revoke**.
  </Step>
</Steps>

Revocation is immediate, and requests with the revoked key return `401 Unauthorized`.

## Station keys

A station key acts only on the station it was issued for. It writes runs for the station's [procedures](/docs/fundamentals/test-data-model#procedure), reads its own configuration, and reports health. It cannot read other stations' data, invite members, or change roles.

### Create a station key

Station keys are issued when you register a station, so you do not create them by hand. The install command embeds a one-hour [setup token](/docs/access/setup-tokens), and the CLI exchanges it for a long-lived station key on first boot. For the full registration flow, see [stations](/docs/station).

### Rotate a station key

<Steps>
  <Step>
    Open **Stations** and pick the station.
  </Step>

  <Step>
    Click **Rotate key**.
  </Step>
</Steps>

The old key is revoked. The CLI detects the revocation on its next request and re-runs setup with a new token.

## Permissions

Keys inherit the permissions of their owner, so you do not configure permissions on the key itself.

* A **user key** carries the user's role, access group assignments, and 2FA-enforcement status.
* A **station key** is scoped to the station's procedures and read-only access to its configuration.

Assign the right [role](/docs/access/access-roles) and [access group](/docs/access/groups), and keys are scoped automatically.

## Audit and rotation

Every key carries `created_at`, `last_used_at`, and `expires_at`. Sort by `last_used_at` to find stale keys, or by `expires_at` for keys nearing expiration.

<Cards>
  <Card icon={<UserCheck />} title="Access roles" href="/docs/access/access-roles" description="Choose one of five roles, from Owner to Operator, to set what a member can do." />

  <Card icon={<Computer />} title="Station" href="/docs/station" description="Install the CLI on a factory bench to run tests unattended." />

  <Card icon={<Code />} title="REST API" href="/docs/api/rest-api" description="REST API endpoint reference." />
</Cards>
