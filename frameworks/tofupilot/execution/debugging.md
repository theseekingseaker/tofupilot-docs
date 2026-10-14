<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/execution/debugging -->
# Debugging
URL: /docs/frameworks/tofupilot/execution/debugging

Learn how to debug Python phase code in TofuPilot by attaching a debugger like VS Code, using debug mode or a manual debugpy listener with breakpoints.

***

title: Debugging
description: Learn how to debug Python phase code in TofuPilot by attaching a debugger like VS Code, using debug mode or a manual debugpy listener with breakpoints.
icon: Bug
---------

import { Tabs, TabsList, TabsTrigger, TabsContent } from 'fumadocs-ui/components/tabs';
import { Callout } from 'fumadocs-ui/components/callout';
import { IconFileCode, IconBrandPython } from '@tabler/icons-react';

You can attach a debugger such as VS Code to your Python phase code and set breakpoints, step through execution, and inspect variables.

Phases run inside a worker process, so debugging uses [debugpy](https://github.com/microsoft/debugpy) over a socket rather than an interactive terminal. Uncaught exceptions and tracebacks already appear in the [logs](/docs/frameworks/tofupilot/logs); a debugger is for stepping through code interactively.

## Debug Mode

Add `debugpy` to your project dependencies:

```bash
uv add debugpy
```

Run with `--debug`:

```bash
tofupilot run --debug
```

The worker starts a debugpy listener and waits for a debugger to attach before running any phase. Debug mode also forces a single worker and disables phase timeouts, so a breakpoint pause is not interrupted.

Attach from VS Code with an "attach" configuration:

```json filename=".vscode/launch.json"
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Attach to TofuPilot",
      "type": "debugpy",
      "request": "attach",
      "connect": { "host": "localhost", "port": 5678 }
    }
  ]
}
```

Set breakpoints in your phase files, run `tofupilot run --debug`, then start the attach configuration. Use `--debug-port` to change the port from the default `5678`.

<Callout>
  Debug mode runs a single worker, so parallel slots run sequentially while debugging. Remove `--debug` for normal parallel execution.
</Callout>

## Manual Attach

You can also start debugpy yourself from inside a phase, without debug mode. This works on any version but you manage the listener, single-worker execution, and timeouts yourself.

<Tabs defaultValue="check_voltage.py">
  <TabsList>
    <TabsTrigger value="check_voltage.py">
      <IconBrandPython className="size-4" />

      check\_voltage.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="check_voltage.py">
    ```python
    import debugpy

    # Start the listener once and wait for the IDE to attach
    if not debugpy.is_client_connected():
        debugpy.listen(("localhost", 5678))
        debugpy.wait_for_client()

    def check_voltage(measurements, multimeter):
        debugpy.breakpoint()
        measurements.voltage = multimeter.measure_dc_voltage()
    ```
  </TabsContent>
</Tabs>

When running manually, set `execution.workers` to `1` so the fixed port doesn't collide across workers, and remove any `timeout` on the phases you are debugging so a breakpoint pause isn't treated as a hung phase.

<Callout>
  Do not combine a manual `debugpy.listen()` with `--debug`. Debug mode starts its own listener, and a second `listen()` on the same port raises an error.
</Callout>
