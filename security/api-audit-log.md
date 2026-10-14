<!-- Source: https://www.tofupilot.com/docs/security/api-audit-log -->
# API Audit Log
URL: /docs/security/api-audit-log

Review API activity by caller, endpoint, time, and status. Understand recorded request inputs, dashboard exports, and access permissions.

***

title: API Audit Log
description: "Review API activity by caller, endpoint, time, and status. Understand recorded request inputs, dashboard exports, and access permissions."
--------------------------------------------------------------------------------------------------------------------------------------------------------

import { Callout } from 'fumadocs-ui/components/callout';
import { Cards, Card } from 'fumadocs-ui/components/card';
import { Key, User, Code } from 'lucide-react';

API activity helps you investigate calls to the [REST API](/docs/api/rest-api), including requests made by users and [Stations](/docs/station). Review the recorded caller, endpoint, outcome, and related data from one place.

## What gets logged

Activity entries can contain the following information. Some fields are present only when the request supplies them or creates a related record.

| Field               | Value                                                                                              |
| ------------------- | -------------------------------------------------------------------------------------------------- |
| Entry ID            | The activity record's unique identifier.                                                           |
| Start and end times | Recorded processing times, in UTC with millisecond precision. Their difference gives the duration. |
| Method and endpoint | The request method and the endpoint being called.                                                  |
| Status              | The recorded response status code.                                                                 |
| Caller              | The user or station associated with the request.                                                   |
| Client and version  | Recognized client information, when available.                                                     |
| Related record      | The run, procedure, unit, revision, or part linked to the call, when available.                    |
| Message and traces  | Diagnostic messages with their level and timestamp, when recorded.                                 |
| Request input       | Small request inputs may be retained, as described below.                                          |

<Callout type="info" title="Request inputs can be stored">
  When a request input is recorded, its serialized JSON is retained if it is no longer than 1,000 characters. Larger inputs are omitted rather than truncated. Consider this when deciding what information to send in API requests.
</Callout>

Activity logging is best-effort: a failure to save an activity entry does not block the original API operation. The activity log is not a guarantee that every request has been recorded.

## Dashboard

Open **API Logs** in the sidebar (Monitor group). You see requests in reverse-chronological order, and the top of the page renders a 144-bucket histogram of request volume over the selected window, stacked by status category (`2xx`, `3xx`, `4xx`, `5xx`). This lets you spot waves of failures or traffic spikes at a glance.

You can filter by:

* **Date range**: preset or custom window. The default is the last month.
* **Endpoint**: narrow the table to a recorded endpoint, such as the run or procedure endpoint.
* **Method**: only `POST`, only `DELETE`, etc.
* **Status**: `2xx`, `3xx`, `4xx`, `5xx` categories.
* **Client**: recognized client information. Python is currently the recorded client category; other requests may have no client label.
* **Created by**: narrow to a specific user or [Station](/docs/station).

Click a row to open the activity details and follow its related [Run](/docs/fundamentals/test-data-model#run), [Unit](/docs/inventory/units), or [Procedure](/docs/fundamentals/test-data-model#procedure), when available.

## Retention

Review your deployment's retention and archiving requirements with the TofuPilot team. If you need records for a longer-term review, keep your own exports according to your organization's policy.

## Export

Use the dashboard export menu to download the currently loaded activity rows as CSV or JSON. Apply the filters you need, then load the rows you want to include. The export contains those loaded rows; it does not fetch every matching record automatically.

Exports contain the entry ID, method, endpoint, status, start and end times, client information, caller names, and message. Request inputs and diagnostic traces are not included in these dashboard exports.

API activity is not currently exposed through a public REST export endpoint.

## Permissions

Access to the audit log inherits the organization's role model, so a member sees what their role allows.

| Role                    | Sees                                    |
| ----------------------- | --------------------------------------- |
| Owner, Admin, Developer | API activity within their organization. |
| Viewer, Operator        | No access.                              |

<Cards>
  <Card icon={<Key />} title="API keys" href="/docs/access/api-keys" description="Create, scope, rotate, revoke." />

  <Card icon={<User />} title="Managing members" href="/docs/access/managing-members" description="Remove a compromised member's access." />

  <Card icon={<Code />} title="REST API" href="/docs/api/rest-api" description="The endpoints being audited." />
</Cards>
