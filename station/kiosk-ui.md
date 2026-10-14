<!-- Source: https://www.tofupilot.com/docs/station/kiosk-ui -->
# Kiosk UI
URL: /docs/station/kiosk-ui

Learn how to boot a TofuPilot station into a full-screen browser with no toolbars or address bar so operators see only the procedure they need to run.

***

title: Kiosk UI
description: "Learn how to boot a TofuPilot station into a full-screen browser with no toolbars or address bar so operators see only the procedure they need to run."
---------------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Callout } from 'fumadocs-ui/components/callout';

In kiosk mode, the CLI starts a local WebSocket server on loopback and launches a Chromium-family browser pointed at the [Operator UI](/docs/frameworks/tofupilot/operator-ui), with no toolbars and no address bar. The browser's lifetime tracks the daemon, so operators see only the procedure UI for as long as the station is running.

There are three operator UI modes in total, so see [Terminal UI](/docs/station/terminal-ui) for the in-shell variant and [Streaming](/docs/station/remote-ui) for remote supervision.

<Callout type="info">
  Kiosk mode needs a graphical display. A station running as a root system service (see [Launch on boot](/docs/station/launch-on-boot)) has no display, so the kiosk cannot open and the dashboard disables this toggle. Control those stations from the dashboard instead.
</Callout>

## Enable kiosk mode

The `kiosk_ui` station config key controls kiosk mode, and you set it at **Station > Configuration > Kiosk UI** on the dashboard. The change syncs on the next config push, and the CLI reads the key on startup.

To override the setting for a single run, pass one of the kiosk flags.

```bash title="terminal"
tofupilot run ./procedure.yaml --kiosk
tofupilot run ./procedure.yaml --no-kiosk
```

When you turn `kiosk_ui` off, the station reverts to a headless service that serves the operator UI URL without opening a browser.

## What happens on station start

When the daemon comes up with kiosk mode on, it walks through four steps before the operator sees the UI.

1. It binds a local WebSocket server on `127.0.0.1` and prints `Kiosk UI: <url>` to the log.
2. It seeds the WebSocket hello frame with the current local deployments so the SPA renders the procedure list immediately.
3. It launches the kiosk-mode browser pointed at the local URL.
4. It holds the browser process in a `KioskHandle` whose `Drop` impl kills the window on daemon shutdown.

## Supported browsers

The CLI probes browsers in a fixed order on each platform and uses the first one it finds.

| Platform | Probe order                                                                                   | Kiosk flag                                                                                                  |
| -------- | --------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| Linux    | `chromium-browser`, `chromium`, `google-chrome`, `microsoft-edge`, `brave-browser`, `firefox` | `--kiosk` (Chromium) / `-kiosk` (Firefox)                                                                   |
| macOS    | `Google Chrome`, `Microsoft Edge`, `Brave`, `Firefox`                                         | `--kiosk` / `-kiosk`                                                                                        |
| Windows  | `msedge.exe`, `chrome.exe`, `firefox.exe`                                                     | `--kiosk` / `-kiosk`, and for Edge also `--edge-kiosk-type=fullscreen` and `--kiosk-idle-timeout-minutes=0` |

Kiosk mode is a presentation mode. It always gives a full-screen window with no toolbars, but it does not switch the rest of the browser off, and some keyboard shortcuts can stay available. The exact behaviour varies a little between browsers and operating systems.

If the CLI finds no kiosk-capable browser, it falls back to the default browser, and window lifetime no longer tracks the daemon.

<Callout type="warn">
  Safari has no CLI kiosk flag and is skipped on macOS, so install Chrome, Edge, or Brave on macOS stations.
</Callout>

## Raspberry Pi: labwc touch fix

Pi OS Bookworm ships labwc in mouse-emulation mode, which makes touch events behave like mouse drags and breaks scrolling and slider controls. To work around this, the CLI patches the labwc config on Linux when kiosk mode is enabled.

On Linux with kiosk mode enabled, the CLI patches `~/.config/labwc/rc.xml` once at startup:

* It flips every `<touch mouseEmulation="yes"/>` to `"no"`.
* It sets `<libinput><mouseEmulation>no</mouseEmulation></libinput>`.
* It writes a `.bak.tofupilot` backup.

The patch is best-effort, so a missing config, a non-labwc compositor, or a parse failure logs a warning and lets the daemon continue.

## Lifecycle

The kiosk window dies with the daemon, and the **Close CLI** button in the operator UI sends an `Exit` station command over the local WebSocket. The CLI catches that command, detaches the kiosk handle synchronously, and exits, and `kill -TERM` produces the same result.
