<!-- Source: https://www.tofupilot.com/docs/ai/mcp -->
# MCP Server
URL: /docs/ai/mcp

Connect Claude, Cursor, and any MCP client to your TofuPilot test data with OAuth. Read, and optionally write, runs, units, parts, batches, procedures, and stations from your AI agent.

***

title: MCP Server
sidebarTitle: MCP
description: "Connect Claude, Cursor, and any MCP client to your TofuPilot test data with OAuth. Read, and optionally write, runs, units, parts, batches, procedures, and stations from your AI agent."
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

The TofuPilot MCP server lets AI agents (Claude, Cursor, and any [Model Context Protocol](https://modelcontextprotocol.io) client) work with your test data directly. Clients authorize through your browser with OAuth, so there is no API key to copy or rotate. Each client is scoped to one organization and, by default, has read-only access; you can grant write access at authorization time. With write access, agents can create and update runs, units, parts, batches, procedures, and stations—including revisions and versions—and manage deployments, logs, and phases.

## Endpoint

```
https://www.tofupilot.app/mcp
```

The server uses the Streamable HTTP transport. Point your client at this URL; it discovers the OAuth flow automatically.

On a [self-hosted](/docs/self-hosting) instance the endpoint is `https://<your-domain>/mcp` and the MCP server is a [license](/docs/pricing/license) feature (`mcp`). When the license does not include it, the endpoint answers 403 and **Settings > MCP** shows the feature as not included.

## Connect

Find these snippets under **Settings → MCP** in your dashboard, then add the server to your client.

### Claude

```bash
claude mcp add --transport http tofupilot https://www.tofupilot.app/mcp
```

Run `/mcp` in Claude to start the browser authorization.

### Cursor

Add to `.cursor/mcp.json`:

```json filename=".cursor/mcp.json"
{
  "mcpServers": {
    "tofupilot": {
      "url": "https://www.tofupilot.app/mcp"
    }
  }
}
```

### Any other client

Use the endpoint URL with Streamable HTTP transport. The client discovers the OAuth authorization server from the URL.

## How authorization works

On first connect, your client opens a browser window:

1. Sign in to TofuPilot.
2. Pick the organization the client should access.
3. Approve the requested access.

A token is issued to the client and stored by it. Each client stays bound to the organization you picked. The token expires and is checked on every request. Revoking a client (below) or being removed from the organization cuts its access.

## Switch a client to a different organization

Each client is bound to one organization. To point an existing client at a different one:

1. Open **Settings → MCP**.
2. Revoke the client under **Connected clients**.
3. Reconnect, and you will be asked to pick an organization again.

## Manage and revoke access

**Settings → MCP** lists every connected client with its bound organization. Revoking a client deletes its tokens and consent; the client stops working immediately and must reconnect to regain access.

## Access levels

Each client gets one of two access levels, chosen on the consent screen:

* **Read** (default): list and fetch data only.
* **Write**: read, plus create, update, and **permanently delete** data. Deletes can cascade (deleting a part removes its revisions, units, and run history) and are not reversible. Leave write off unless the agent needs it.

You can see each client's access level under **Settings → MCP** and revoke it at any time.

## Read tools

Available to every client. Scoped to the authorized organization.

| Tool                                                          | Description                                                                                           |
| ------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| `runs_list`, `runs_get`                                       | List test runs (filter by outcome, part, serial, date) or fetch one by id.                            |
| `units_list`, `units_get`                                     | List units (filter by serial, part, batch, outcome) or fetch one by id.                               |
| `parts_list`, `parts_get`, `parts_revision_get`               | List parts (search by name or number), fetch one, or fetch a revision.                                |
| `batches_list`, `batches_get`                                 | List batches (filter by part or date) or fetch one by id.                                             |
| `procedures_list`, `procedures_get`, `procedures_version_get` | List procedures, fetch one, or fetch a version by tag.                                                |
| `stations_list`, `stations_get`                               | List test stations or fetch one by id.                                                                |
| `users_list`                                                  | List organization members (id, name, role, access groups).                                            |
| `teams_list`, `teams_get`, `teams_list_members`               | List the access groups in your organization, fetch one by id, or list its members.                    |
| `logs_list`, `logs_get`                                       | List test logs (filter by level, run, procedure, unit, source) or fetch one by id.                    |
| `phases_list`, `phases_get`                                   | List test phases (filter by outcome, unit, run details) or fetch one by id.                           |
| `measurements_list`, `measurements_get`                       | List measurements (filter by phase, value, outcome, unit, run details) or fetch one by id.            |
| `deployments_list`, `deployments_get`                         | List deployments (filter by procedure, environment, build status, branch, author) or fetch one by id. |

## Write tools

Available only to clients granted write access. Destructive tools (delete, remove) require an explicit confirmation argument.

| Tool                                                                      | Description                                                                                                                                                                               |
| ------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `runs_create`, `runs_update_metadata`                                     | Create a test run, or upsert run metadata.                                                                                                                                                |
| `runs_delete`                                                             | Delete one or more test runs by id. Irreversible.                                                                                                                                         |
| `units_create`, `units_update`                                            | Create or update a unit.                                                                                                                                                                  |
| `units_add_child`, `units_remove_child`                                   | Add or remove a sub-unit (assembly) by serial number.                                                                                                                                     |
| `units_delete`                                                            | Delete one or more units by serial number. Cascades to their run history and attachments. Irreversible.                                                                                   |
| `parts_create`, `parts_update`, `parts_delete`                            | Create, update, or delete a part. Delete cascades.                                                                                                                                        |
| `parts_revision_create`, `parts_revision_update`, `parts_revision_delete` | Create, update, or delete a part revision.                                                                                                                                                |
| `batches_create`, `batches_update`, `batches_delete`                      | Create, update, or delete a batch.                                                                                                                                                        |
| `batches_add_units`, `batches_remove_units`                               | Add units to a batch by serial number, or unlink them (the units are not deleted).                                                                                                        |
| `procedures_create`, `procedures_update`, `procedures_delete`             | Create, update, or delete a procedure.                                                                                                                                                    |
| `procedures_version_create`, `procedures_version_delete`                  | Create or delete a procedure version.                                                                                                                                                     |
| `stations_create`, `stations_update`, `stations_remove`                   | Create, update, or remove a station.                                                                                                                                                      |
| `users_invite`                                                            | Invite a user to your organization by email, with a role (owner, admin, developer, viewer, operator). Sends the invitation when email delivery is configured and returns the invite link. |
| `teams_create`, `teams_update`, `teams_delete`                            | Create an access group, rename it, or delete it. Deleting also removes all its memberships.                                                                                               |
| `teams_add_member`, `teams_remove_member`                                 | Add an organization member to an access group, or remove one.                                                                                                                             |

## What the agent can see

The agent only accesses data in the organization you authorize, enforced server-side on every request. Member email addresses and account status are not exposed over MCP. Every write is recorded for audit, and you can revoke a client at any time.
