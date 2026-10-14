<!-- Source: https://www.tofupilot.com/docs/station/auto-update -->
# Auto-update
URL: /docs/station/auto-update

Install TofuPilot CLI updates automatically on every station at launch and between tests so the fleet stays on the latest release without manual intervention.

***

title: Auto-update
description: "Install TofuPilot CLI updates automatically on every station at launch and between tests so the fleet stays on the latest release without manual intervention."
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------

When **Auto-update** is on, the CLI installs new releases at launch and between tests. Stations stay on the latest version without anyone running [`tofupilot update`](/docs/cli/update) by hand.

## Toggle the setting

Open **Stations → *Station name* → Setup** and flip **Auto-update**. The dashboard pushes the new value to the bench over the live channel, and the CLI applies it on receipt.

## When the CLI updates

The CLI checks for a newer release in two windows so the upgrade never interrupts a run:

* **At launch.** The first thing the daemon does on boot is check for a newer release. If one is available, it downloads and swaps the binary before starting the operator UI.
* **Between tests.** When the bench finishes a run and is idle, the CLI checks again. If a release is waiting, it installs and restarts in place.

The CLI never updates mid-run. A long-running procedure holds the previous binary until it completes.

## Manual updates

Flipping **Auto-update** off leaves the CLI on its current version. Update manually with [`tofupilot update`](/docs/cli/update) or pin to a specific version through your deployment pipeline.

## Rolling back

If an update breaks the bench, [`tofupilot rollback`](/docs/cli/rollback) restores the previous version without losing credentials or queued runs.
