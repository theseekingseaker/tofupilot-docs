<!-- Source: https://www.tofupilot.com/docs/cli/service -->
# tofupilot service
URL: /docs/cli/service

Control the TofuPilot station daemon: start, stop, and inspect status under systemd on Linux or launchd on macOS.

***

title: tofupilot service
description: "Control the TofuPilot station daemon: start, stop, and inspect status under systemd on Linux or launchd on macOS."
sidebarTitleCode: tofupilot service
-----------------------------------

The `service` command manages the running station daemon (`systemd` on Linux, `launchd` on macOS), which `tofupilot login --token` puts under supervision when you set up a station.

## `start`

The `start` subcommand runs the daemon in the foreground, and `systemd` and `launchd` use it as the `ExecStart` target. You rarely call it by hand.

```bash title="service start"
tofupilot service start
```

## `stop`

The `stop` subcommand stops the running daemon.

```bash title="service stop"
tofupilot service stop
```

## `status`

The `status` subcommand probes the loopback port the operator UI binds, then shows the supervisor's view, the resolved port, any `TOFUPILOT_LOCAL_UI_PORT` override, and log locations.

```bash title="service status"
tofupilot service status
```

The exit code follows one contract: 0 if and only if the operator UI socket is accepting on the resolved port, so scripts can rely on `exit==0` meaning the daemon is live. With `--json`, a dead socket always exits 1; in human mode the CLI propagates the supervisor's non-zero exit code when available (systemd uses 3 for inactive and 4 for not installed), clamped to 1 when the supervisor claims success despite the dead socket.
