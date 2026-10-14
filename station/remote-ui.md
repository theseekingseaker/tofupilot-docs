<!-- Source: https://www.tofupilot.com/docs/station/remote-ui -->
# Remote UI
URL: /docs/station/remote-ui

Watch and drive a running TofuPilot station from the dashboard in real time so anyone with access can supervise procedures, answer prompts, and step in remotely.

***

title: Remote UI
description: "Watch and drive a running TofuPilot station from the dashboard in real time so anyone with access can supervise procedures, answer prompts, and step in remotely."
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

The Operator UI mirrors a running [Station](/docs/station) into the dashboard. The view renders the same procedure flow, prompts, and measurement readouts the operator sees on the bench, with sub-second latency, and members with the right access can answer prompts directly from the browser.

Open it from **Stations → *Station name* → Operate**, or jump straight to `/<organization>/operator/<station>`. A single-station organization skips the picker and lands on the only bench.

## What the dashboard shows

* The active procedure, version, and unit under test.
* Every phase outcome as it lands.
* Live measurement readouts and limits.
* Operator prompts waiting for input.
* Elapsed time and queue status when the bench is offline.

## How it works

The CLI publishes operator UI state to a WebSocket channel scoped to the station. The dashboard subscribes to that same channel through a short-lived JWT, and the server replays the last snapshot on connect so the page renders immediately without waiting for the next event.

A reverse channel carries dashboard input back to the bench. Members with write access to the station can:

* Answer an operator prompt from the browser.
* Cancel a paused run.
* Trigger a station-level command from the **Station configuration** panel.

Write access goes only to members whose role and [access group scope](/docs/access/groups) include the station; everyone else watches read-only.

## Identity

| Caller          | Channel scope                                                                                                     |
| --------------- | ----------------------------------------------------------------------------------------------------------------- |
| Station API key | Publishes to a channel keyed on the station ID. Anyone with read access subscribes.                               |
| User session    | Subscribes through a JWT signed for the user's organization. Cannot cross to a different organization's channels. |

The JWT is short-lived and refreshes transparently on both sides.

## Permissions

| Role         | Operator UI                                           |
| ------------ | ----------------------------------------------------- |
| Owner, Admin | Watch and drive any station.                          |
| Developer    | Watch and drive any station.                          |
| Viewer       | Watch stations in their access group scope. No write. |
| Operator     | Drives the station they sign in to from the bench.    |
