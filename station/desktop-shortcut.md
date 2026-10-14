<!-- Source: https://www.tofupilot.com/docs/station/desktop-shortcut -->
# Desktop shortcut
URL: /docs/station/desktop-shortcut

Place a TofuPilot shortcut on the station desktop so operators can launch a test run with a single click, with no terminal commands or browser bookmarks.

***

title: Desktop shortcut
description: "Place a TofuPilot shortcut on the station desktop so operators can launch a test run with a single click, with no terminal commands or browser bookmarks."
------------------------------------------------------------------------------------------------------------------------------------------------------------------------

When **Desktop shortcut** is on, the CLI writes a launcher to the station desktop. A double-click on the icon opens the operator UI in whichever mode the station is configured for (Terminal UI, Kiosk UI, or both).

## Toggle the setting

Open **Stations → *Station name* → Setup** and flip **Desktop shortcut**. The dashboard pushes the new value to the bench, and the CLI writes the icon on receipt.

## What the shortcut does

The shortcut points at the CLI binary with the bench's working directory baked in, so launching it is equivalent to opening a terminal, navigating to the station directory, and running `tofupilot run`. The bench then starts the configured UI:

* [Kiosk UI](/docs/station/kiosk-ui) opens a full-screen browser pointed at the operator UI.
* [Terminal UI](/docs/station/terminal-ui) renders the procedure flow in the terminal that the shortcut spawns.
* With both off, the bench runs headless and the operator drives it from the dashboard.

## Per-platform behavior

| Platform | File written                                                                |
| -------- | --------------------------------------------------------------------------- |
| Linux    | `~/Desktop/TofuPilot.desktop` (XDG `.desktop` entry, marked trusted).       |
| macOS    | `~/Desktop/TofuPilot.command` shell launcher with execute permissions.      |
| Windows  | `%USERPROFILE%\Desktop\TofuPilot.lnk` shortcut pointing at `tofupilot.exe`. |

## Turning it off

When you flip the toggle off, the CLI deletes the shortcut from the desktop. Operators keep launching the bench from the terminal or from [Launch on boot](/docs/station/launch-on-boot) if it's enabled.
