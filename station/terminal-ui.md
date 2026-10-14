<!-- Source: https://www.tofupilot.com/docs/station/terminal-ui -->
# Terminal UI
URL: /docs/station/terminal-ui

Learn how to render the TofuPilot operator UI directly in the terminal so a station with no browser or display server can still drive procedures.

***

title: Terminal UI
description: "Learn how to render the TofuPilot operator UI directly in the terminal so a station with no browser or display server can still drive procedures."
----------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Callout } from 'fumadocs-ui/components/callout';

The terminal UI shows the operator UI in the same terminal that runs the CLI, so you need no browser, no display server, and no graphical environment. Every prompt, measurement, and phase status updates live in the same shell.

There are three operator UI modes in total, so see [Kiosk](/docs/station/kiosk-ui) for the full-screen browser and [Streaming](/docs/station/remote-ui) for remote supervision.

## Enable the TUI

The `terminal_ui` station config key controls the TUI, and you set it at **Station > Configuration > Terminal UI** on the dashboard. The change syncs on the next config push, and the CLI reads the key when a run starts.

To override the setting for a single run, pass one of the TUI flags.

```bash title="terminal"
tofupilot run ./procedure.yaml --tui        # force on
tofupilot run ./procedure.yaml --no-tui     # force off
```

The CLI resolves the value in this order: CLI flag, then station config, then the default (`off`). The `--tui` and `--no-tui` flags cannot be combined.

## What the TUI renders

The TUI maps each operator UI component to a terminal equivalent, and a few rich components fall back to text.

| Component                              | Behavior                                                         |
| -------------------------------------- | ---------------------------------------------------------------- |
| Text, number, slider, switch inputs    | Keyboard field that blocks on submit until enter.                |
| Radio, select, checklist, multi-select | Keyboard list.                                                   |
| Image choice, image checklist          | Label-based selection with images referenced by name.            |
| Live text, progress                    | Reactive, and rerenders on `ui.<key> = value`.                   |
| Image display                          | No bitmap rendering, so the panel shows the file path and count. |

Phase status, measurements, logs, and timing all stream into the same screen, and SIGWINCH resizes the layout.

## Limits

A few capabilities are not available in the terminal, so plan operator workflows accordingly.

* No image rendering, so only the label fallback is shown.
* No multi-touch.
* Plain text only, so prompts have no HTML or markdown styling.
* No video.

<Callout type="info">
  TUI and kiosk are not mutually exclusive in code, so enable one per station. If both are on, kiosk takes operator input and the TUI shows read-only output.
</Callout>
