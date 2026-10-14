<!-- Source: https://www.tofupilot.com/docs/station/launch-on-boot -->
# Launch on boot
URL: /docs/station/launch-on-boot

Configure a TofuPilot station to start the CLI in the background as soon as the machine boots so operators can run tests without launching anything manually.

***

title: Launch on boot
description: "Configure a TofuPilot station to start the CLI in the background as soon as the machine boots so operators can run tests without launching anything manually."
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------

When **Launch on boot** is on, the CLI starts as a background service the moment the station boots. The bench is ready to pull deployments and accept runs without anyone opening a terminal.

## Toggle the setting

Open **Stations → *Station name* → Setup** and flip **Launch on boot**. The dashboard pushes the new value to the bench over the live channel, and the CLI applies it on receipt.

## How it works

The CLI registers itself with the platform's service manager when the toggle flips on:

* **Linux (normal user)**: a `systemd` user unit that runs the CLI under the logged-in user. This is the setup for a bench with a screen, where the kiosk opens a browser on the local display.
* **Linux (root)**: a `systemd` system unit at `/etc/systemd/system/tofupilot.service`, started at boot with no login required. The CLI detects that it runs as root and installs the system unit automatically. Run as root when the test sequence needs direct hardware access.
* **macOS**: a `launchd` `LaunchAgent` plist.
* **Windows**: a Run-key entry under `HKCU\Software\Microsoft\Windows\CurrentVersion\Run`.

The CLI installs this service automatically when you set up the bench as a station with [`tofupilot login --token`](/docs/cli/login). The dashboard toggle is the recommended way to manage it across a fleet.

### Running as root on a headless bench

A root system service starts at boot with no login session, so it has no graphical display. The on-screen [kiosk UI](/docs/station/kiosk-ui) cannot open there, and the dashboard disables the kiosk toggle for these stations. Drive the station from the dashboard instead: open **Stations → *Station name*** in any browser to select a procedure and run tests.

Authenticate the root service with `sudo tofupilot login` so credentials are stored where the boot service reads them. A procedure appears in the picker once it is linked to the station and its deployment has been pulled.

If you enable launch on boot from a shell with no login session bus (for example over SSH as a non-root user), `systemctl --user` has no bus to connect to. Either run as root for the system service, or enable lingering for the station user with `sudo loginctl enable-linger <user>`.

## Turning it off

When you flip the toggle off, the CLI deregisters the service entry and removes the boot hook. The CLI keeps running for the current session; the next reboot starts it only when the operator launches it manually.
