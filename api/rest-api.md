<!-- Source: https://www.tofupilot.com/docs/api/rest-api -->
# REST API
URL: /docs/api/rest-api

Call the TofuPilot REST API directly to drive runs, units, procedures, and stations from any environment.

***

title: REST API
description: "Call the TofuPilot REST API directly to drive runs, units, procedures, and stations from any environment."
------------------------------------------------------------------------------------------------------------------------

Interact programmatically with your TofuPilot account using the SDKs or direct HTTP requests. For example, create test runs directly, list and filter units, get user data, and much more.

## API basics

The API is exposed as an HTTP/1 and HTTP/2 service over SSL. All endpoints live under the URL `https://www.tofupilot.app/api/v2` and follow the REST architecture.

## Authentication

TofuPilot API keys are required to authenticate and use the TofuPilot API. Include the key in the `Authorization` header:

```
Authorization: Bearer <API_KEY>
```

Create and manage API keys in your [account settings](/docs/access/api-keys).

## Filtering and pagination

List endpoints accept query parameters to narrow results. Filter, sort, and paginate by appending parameters to the URL:

```
https://www.tofupilot.app/api/v2/units?limit=50&serialNumber=SN-001
```

Refer to each endpoint's reference for the full list of supported filters.

## Error codes

Errors are returned with a standard HTTP status code and a JSON body:

```json
{
  "message": "Human-readable description of the error",
  "code": "NOT_FOUND"
}
```

| HTTP  | Code                    | Meaning                                                                                                                         |
| ----- | ----------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| `400` | `BAD_REQUEST`           | The request body or query parameters are invalid. Input validation errors also include an `issues` array.                       |
| `401` | `UNAUTHORIZED`          | The API key is missing or invalid.                                                                                              |
| `403` | `FORBIDDEN`             | The API key does not have permission for this resource.                                                                         |
| `404` | `NOT_FOUND`             | The endpoint or resource does not exist.                                                                                        |
| `408` | `TIMEOUT`               | A read query ran longer than the 20 second statement limit and was stopped. Narrow the date range or the filters and try again. |
| `409` | `CONFLICT`              | The request collides with current state (for example, a duplicate serial number).                                               |
| `422` | `UNPROCESSABLE_CONTENT` | The payload is syntactically valid but semantically rejected.                                                                   |
| `429` | `TOO_MANY_REQUESTS`     | Rate limit exceeded.                                                                                                            |
| `500` | `INTERNAL_SERVER_ERROR` | An unexpected server error occurred.                                                                                            |

Input validation failures return a `BAD_REQUEST` with the offending fields in `issues`:

```json
{
  "message": "Input validation failed",
  "code": "BAD_REQUEST",
  "issues": [
    {
      "path": ["serialNumber"],
      "message": "Required"
    }
  ]
}
```

Refer to each endpoint's reference for the exact set of errors it can return.

## Rate limits

The API limits the number of calls you can make over a period of time. Requests beyond the limit return `429 TOO_MANY_REQUESTS`. [Contact us](mailto:support@tofupilot.com) if you need higher limits for production workloads.

## Endpoints

Every endpoint is grouped by resource in the sidebar. The most frequently used ones are listed below.

import { Cards, Card } from 'fumadocs-ui/components/card';

export const Method = ({ children }) => {
  const colors = {
    GET: 'text-green-600 dark:text-green-400',
    POST: 'text-blue-600 dark:text-blue-400',
    PUT: 'text-yellow-600 dark:text-yellow-400',
    PATCH: 'text-orange-600 dark:text-orange-400',
    DELETE: 'text-red-600 dark:text-red-400',
  };
  return <span className={`font-mono font-medium ${colors[children] || ''}`}>{children}</span>;
};

<Cards>
  <Card title={<>Create a run <Method>POST</Method></>} href="/docs/api/rest-api/runs/create" description="Stream a finished test run from a station or script." />

  <Card title={<>List runs <Method>GET</Method></>} href="/docs/api/rest-api/runs/list" description="Retrieve runs with filters and pagination." />

  <Card title={<>Create a unit <Method>POST</Method></>} href="/docs/api/rest-api/units/create" description="Register a new unit by serial number." />

  <Card title={<>List units <Method>GET</Method></>} href="/docs/api/rest-api/units/list" description="Retrieve units across procedures and batches." />

  <Card title={<>Attach file to a run <Method>POST</Method></>} href="/docs/api/rest-api/runs/attach_create" description="Upload reports, logs, or images to a run." />

  <Card title={<>Attach file to a unit <Method>POST</Method></>} href="/docs/api/rest-api/units/attach_create" description="Upload datasheets or photos directly to a unit." />
</Cards>
