<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/plugs -->
# Plugs
URL: /docs/frameworks/tofupilot/plugs

Create persistent resources like equipment drivers and database connections for your test phases to access. Reuse plugs across procedures.

***

title: Plugs
description: Create persistent resources like equipment drivers and database connections for your test phases to access. Reuse plugs across procedures.
icon: Plug
----------

import { DynamicCodeBlock } from '@/components/themed-dynamic-code-block';
import { Tabs, TabsList, TabsTrigger, TabsContent } from 'fumadocs-ui/components/tabs';
import { IconFileCode, IconBrandPython } from '@tabler/icons-react';
import { File, Folder, Files } from 'fumadocs-ui/components/files';
import { PhaseFlow } from '@/components/phase-flow';
import { Callout } from 'fumadocs-ui/components/callout';
import { InlineOutcomeBadge } from '@/components/inline-outcome-badge';
import { PlugsOverviewPreview } from '@/components/PlugsOverviewPreview';

## Overview \[toc]

<PlugsOverviewPreview />

<div className="sr-only" aria-hidden="false">
  ```
  Plugs Overview (long-lived resources, per-slot status)
    Keysight DAQ        | active
    EtherCAT Manager    | initializing
    Rigol Power Supply  | idle
  ```
</div>

You can create plugs to manage persistent resources like test equipment or hardware interfaces.

TofuPilot Framework handles plug initialization, lifecycle management, and automatically passes plug instances to your phase functions.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>

    <TabsTrigger value="plug.py">
      <IconBrandPython className="size-4" />

      plug.py
    </TabsTrigger>

    <TabsTrigger value="phase.py">
      <IconBrandPython className="size-4" />

      phase.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    plugs: # [!code ++]
      - name: EtherCAT Manager # [!code ++]
        python: hardware.ethercat:EtherCATManager # [!code ++]

    main:
      - name: Test Motor
        python: phases.motor:test_motor
    ```
  </TabsContent>

  <TabsContent value="plug.py">
    ```python
    class EtherCATManager:
        def __init__(self):
            # Initialize connection
            pass

        def send_command(self, cmd):
            # Send command to hardware
            pass
    ```
  </TabsContent>

  <TabsContent value="phase.py">
    ```python
    def test_motor(ethercat_manager):
        ethercat_manager.send_command("START")
    ```
  </TabsContent>
</Tabs>

Plugs are standard Python classes.

## Name

You can create a new plug by adding it to the `plugs` list.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    plugs:
      - name: EtherCAT Manager # [!code ++]
    ```
  </TabsContent>
</Tabs>

TofuPilot will automatically trim whitespace and enforce a 100 character limit.

## Description

You can add an optional description to explain what the plug is.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    plugs:
      - name: EtherCAT Manager
        description: "Manages EtherCAT communication with motors" # [!code ++]
    ```
  </TabsContent>
</Tabs>

TofuPilot will enforce a 50,000 character limit on descriptions.

## Key

You can define a key for referencing this plug in your phase function parameters.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    plugs:
      - name: EtherCAT Manager
        description: "Manages EtherCAT communication with motors"
        key: ethercat_manager # [!code ++]
    ```
  </TabsContent>
</Tabs>

If not specified, TofuPilot auto-generates a key from the plug name. Keys must be valid Python identifiers: start with a letter or underscore, followed by letters, numbers, or underscores.

## Python

You can define plugs as standard Python classes.

<Tabs defaultValue="power_supply.py">
  <TabsList>
    <TabsTrigger value="power_supply.py">
      <IconBrandPython className="size-4" />

      power\_supply.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="power_supply.py">
    ```python
    class PowerSupply:
        def __init__(self):
            self.address = "192.168.1.100"

        def __del__(self):
            self.write("OUTP OFF")

        def set_voltage(self, volts):
            self.write(f"VOLT {volts}")

        def write(self, command):
            pass  # Actual hardware communication here
    ```
  </TabsContent>
</Tabs>

And reference the Python class in YAML:

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    plugs:
      - name: Power Supply
        description: "Programmable power supply"
        python: instruments.power_supply:PowerSupply # [!code ++]
    ```
  </TabsContent>
</Tabs>

TofuPilot will automatically initialize the plug at setup and ensure its destruction at teardown.

## Config

You can pass a `config` mapping to the plug. Each key becomes a keyword argument on the plug class `__init__`, so you can keep instrument addresses and settings in the procedure instead of hard-coding them in Python.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>

    <TabsTrigger value="power_supply.py">
      <IconBrandPython className="size-4" />

      power\_supply.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    plugs:
      - name: Power Supply
        python: instruments.power_supply:PowerSupply
        config: # [!code ++]
          address: "192.168.1.100" # [!code ++]
    ```
  </TabsContent>

  <TabsContent value="power_supply.py">
    ```python
    class PowerSupply:
        def __init__(self, address): # [!code ++]
            self.address = address
    ```
  </TabsContent>
</Tabs>

Config values must be JSON-serializable. If the keys don't match the `__init__` signature, Python raises a `TypeError` that TofuPilot reports as a plug initialization error.

### Multiple instances

Register the same class more than once, each with its own `key` and `config`. Each entry is an independent instance, injected into phases by its key — useful for several instruments of the same model.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>

    <TabsTrigger value="phase.py">
      <IconBrandPython className="size-4" />

      phase.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    name: Power Supply Test

    plugs:
      - name: Rigol A
        key: rigol_a
        python: instruments.rigol:RigolDP832
        config:
          address: "192.168.1.100"
      - name: Rigol B
        key: rigol_b
        python: instruments.rigol:RigolDP832
        config:
          address: "192.168.1.101"

    main:
      - name: Test Both
        python: phases.test_both
    ```
  </TabsContent>

  <TabsContent value="phase.py">
    ```python
    def test_both(rigol_a, rigol_b):
        rigol_a.set_voltage(3.3)
        rigol_b.set_voltage(5.0)
    ```
  </TabsContent>
</Tabs>

## Scope

The `scope` field sets how long a plug instance lives and which phases share it. Three values, smallest lifetime to largest:

| Scope            | Instance                | Lifetime                                                                       |
| ---------------- | ----------------------- | ------------------------------------------------------------------------------ |
| `slot` (default) | one per slot            | created for the slot, destroyed with it                                        |
| `execution`      | one shared by all slots | created when the execution starts, destroyed when it ends                      |
| `station`        | one per station         | created on first use, held across executions, destroyed when the station stops |

A slot corresponds to one unit under test — and to one run on the dashboard. An execution is one pass of the whole procedure over every slot; a multi-slot execution produces one dashboard run per slot, which is why this scope is named `execution` and not `run`.

```yaml
plugs:
  - name: Power Supply
    python: instruments.power_supply:PowerSupply
    scope: station # [!code ++]
    config:
      address: "192.168.1.100"
```

`slot` and `execution` differ on [multi-slot procedures](/docs/frameworks/tofupilot/execution/slots): a `slot` plug is instantiated once per unit under test, an `execution` plug once for all of them together. On single-slot procedures they behave identically.

`station` keeps the instrument connection open between executions. The plug `__init__` runs once when the first execution needs it; every following execution reuses the same live instance, and teardown happens when the station stops or a new deployment is applied. Use it for connections that are slow to establish (VISA sessions, TCP links, serial ports) so back-to-back units don't pay the reconnection cost.

Station scope requires [station mode](/docs/station) — the station process owns the instance. In a one-shot `tofupilot run`, a `station` plug behaves as `execution` scope.

The held instance is tied to the deployed procedure version: updating the deployment releases every held instance, and the next execution reconnects using the new bundle — plug code changes always take effect on the first run after a deploy. Within an unchanged deployment, the engine health-checks the plug process before each reuse and respawns it if it died.

If the connection to the instrument itself drops mid-test, handle the reconnect inside your plug class — the engine guarantees a long-lived plug process, not a live instrument session.

### Concurrency

A plug instance runs one method call at a time. When several slots call the same `execution` or `station` plug, their calls queue and execute one at a time, like commands on a SCPI socket. Keep plug methods short and put long waits in phases: a method that sleeps for minutes holds every other slot's calls to that plug for as long as it runs.

The legacy spellings `each` (for `slot`) and `all` (for `execution`) still parse.

## Setup / Teardown

### Single Slot

TofuPilot automatically manages your plug lifecycle during execution:

1. Initialize plugs by calling `__init__()` method
2. Execute procedure phases
3. Destroy plugs by calling `__del__()` method

<PhaseFlow
  nodes={[
  { id: 'start', type: 'start', position: { x: 340, y: 0 }, data: { label: 'Start' } },
  { id: 'init', type: 'plug', position: { x: 250, y: 100 }, data: { label: 'Plug Initialization', outcome: 'pass', lifecycle: 'init' } },
  { id: 'main', type: 'phase', position: { x: 250, y: 200 }, data: { label: 'Procedure Phases', outcome: 'pass' } },
  { id: 'del', type: 'plug', position: { x: 250, y: 300 }, data: { label: 'Plug Deletion', outcome: 'pass', lifecycle: 'teardown' } },
  { id: 'end', type: 'end', position: { x: 340, y: 400 }, data: { label: 'End' } },
]}
  edges={[
  { id: 'e-start-init', source: 'start', target: 'init' },
  { id: 'e-init-main', source: 'init', target: 'main' },
  { id: 'e-main-del', source: 'main', target: 'del' },
  { id: 'e-del-end', source: 'del', target: 'end' },
]}
  height="520px"
/>

If your procedure specifies [Setup or Teardown Phases](/docs/frameworks/tofupilot/phases/setup-teardown), TofuPilot initializes plugs before setup and destroys them after teardown.

<PhaseFlow
  nodes={[
  { id: 'start', type: 'start', position: { x: 340, y: 0 }, data: { label: 'Start' } },
  { id: 'init', type: 'plug', position: { x: 250, y: 100 }, data: { label: 'Plug Initialization', outcome: 'pass', lifecycle: 'init' } },
  { id: 'setup', type: 'phase', position: { x: 250, y: 200 }, data: { label: 'Setup Phases', outcome: 'pass' } },
  { id: 'main', type: 'phase', position: { x: 250, y: 300 }, data: { label: 'Main Phases', outcome: 'pass' } },
  { id: 'teardown', type: 'phase', position: { x: 250, y: 400 }, data: { label: 'Teardown Phases', outcome: 'pass' } },
  { id: 'del', type: 'plug', position: { x: 250, y: 500 }, data: { label: 'Plug Deletion', outcome: 'pass', lifecycle: 'teardown' } },
  { id: 'end', type: 'end', position: { x: 340, y: 600 }, data: { label: 'End' } },
]}
  edges={[
  { id: 'e-start-init', source: 'start', target: 'init' },
  { id: 'e-init-setup', source: 'init', target: 'setup' },
  { id: 'e-setup-main', source: 'setup', target: 'main' },
  { id: 'e-main-teardown', source: 'main', target: 'teardown' },
  { id: 'e-teardown-del', source: 'teardown', target: 'del' },
  { id: 'e-del-end', source: 'del', target: 'end' },
]}
  height="720px"
/>

TofuPilot will stop the execution immediately on plug initialization <InlineOutcomeBadge outcome="error" />, even before executing setup phases, but will always destroy plugs to prevent resource leaks no matter the execution status.

## Plug Design

### Errors

You can raise exceptions in plugs like you would typically do in Python.

<Tabs defaultValue="rigol.py">
  <TabsList>
    <TabsTrigger value="rigol.py">
      <IconBrandPython className="size-4" />

      rigol.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="rigol.py">
    ```python
    class RigolDP832:
        def set_voltage(self, channel, voltage):
            if voltage > self.max_voltage:
                raise ValueError(f"Voltage {voltage}V exceeds maximum {self.max_voltage}V") # [!code ++]
            cmd = f":SOUR{channel}:VOLT {voltage}\n"
            self.socket.send(cmd.encode())
    ```
  </TabsContent>
</Tabs>

TofuPilot will automatically capture and report errors.

### Logs

You can use print statements for plug-level logging.

<Tabs defaultValue="rigol.py">
  <TabsList>
    <TabsTrigger value="rigol.py">
      <IconBrandPython className="size-4" />

      rigol.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="rigol.py">
    ```python
    class RigolDP832:
        def measure_voltage(self, channel):
            cmd = f":MEAS:VOLT? CH{channel}\n"
            self.socket.send(cmd.encode())
            voltage = self.socket.recv(1024).decode().strip()
            print(f"Measured: {voltage}V") # [!code ++]
            return float(voltage)
    ```
  </TabsContent>
</Tabs>

TofuPilot will capture and report them.

### Phase-Based Management

You can use dedicated setup and teardown phases for complex plug management to leverage TofuPilot native phase execution features like UI prompts, timeouts and more.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>

    <TabsTrigger value="rigol.py">
      <IconBrandPython className="size-4" />

      rigol.py
    </TabsTrigger>

    <TabsTrigger value="connect_psu.py">
      <IconBrandPython className="size-4" />

      connect\_psu.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    plugs:
      - name: Power Supply
        python: instruments.rigol:RigolDP832

    setup: # [!code ++]
      - name: Connect Power Supply # [!code ++]
        python: setup.connect_psu # [!code ++]
        timeout: 30s # [!code ++]
        ui: # [!code ++]
          components: # [!code ++]
            - key: status # [!code ++]
              type: text # [!code ++]
              label: "Connection Status" # [!code ++]

    teardown:
      - name: Disconnect Power Supply
        python: teardown.disconnect_psu
    ```
  </TabsContent>

  <TabsContent value="rigol.py">
    ```python
    class RigolDP832:
        def __init__(self):
            # Minimal initialization
            self.socket = None

        def connect(self, ip, port=5555):
            # Complex connection logic called from phase
            self.socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
            self.socket.connect((ip, port))
            return True

        def disconnect(self):
            if self.socket:
                self.socket.close()
    ```
  </TabsContent>

  <TabsContent value="connect_psu.py">
    ```python
    def connect_psu(power_supply, phase, ui, log):
        ui.status = "Connecting to power supply..."

        if power_supply.connect("192.168.1.50"):
            log.info("Power supply connected successfully")
        else:
            phase.fail("Failed to connect to power supply")
    ```
  </TabsContent>
</Tabs>

This approach gives you full phase capabilities for plug management operations.

### Garbage Collection

TofuPilot guarantees cleanup by calling `__del__()` at plug deletion and terminating the subprocess, unlike Python's non-deterministic garbage collector.

### Method Calling

Plugs are automatically passed to phase functions by matching parameter names (case-insensitive) to plug keys.

<Tabs defaultValue="test_motor.py">
  <TabsList>
    <TabsTrigger value="test_motor.py">
      <IconBrandPython className="size-4" />

      test\_motor.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="test_motor.py">
    ```python
    def test_motor(ethercat_manager):
        ethercat_manager.send_command("START")  # Call plug methods directly
    ```
  </TabsContent>
</Tabs>

TofuPilot runs plugs in a separate process from phases and uses a TCP server to handle method calls. When you call `ethercat_manager.send_command("START")`, TofuPilot serializes the method name and arguments, sends them to the plug process, executes the method, and returns the result.

**Benefits:**

* Process isolation prevents plug crashes from affecting phases
* Plug resources (sockets, file handles) stay in one process
* Automatic cleanup when plug process terminates

**Limitations:**

You can only pass serializable arguments (strings, numbers, lists, dicts) to plug methods, and arguments must be passed positionally — keyword arguments are not supported. You cannot pass or return plug instances, functions, or other non-serializable objects:

<Tabs defaultValue="measure_voltage.py">
  <TabsList>
    <TabsTrigger value="measure_voltage.py">
      <IconBrandPython className="size-4" />

      measure\_voltage.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="measure_voltage.py">
    ```python
    def measure_voltage(power_supply, measurements):
        # ✅ Pass serializable arguments positionally, get serializable results
        voltage = power_supply.measure_voltage(1)
        measurements.voltage = voltage

        # ❌ Cannot use keyword arguments
        # power_supply.measure_voltage(channel=1)

        # ❌ Cannot pass plug instance to another plug
        # other_device.configure(power_supply)

        # ❌ Cannot return plug instance
        # return power_supply
    ```
  </TabsContent>
</Tabs>

<Callout type="info" title="We're looking for your feedback">
  Share your experience with plugs and help shape future improvements. Join the discussion on [Discord](https://discord.gg/tofupilot).
</Callout>

## Example

Let's create a power supply plug to control voltage output:

1. Define the plug in procedure YAML
2. Implement the plug class
3. Use it in a phase

We'll create the procedure file and the Python modules:

<Files>
  <File name="procedure.yaml" />

  <Folder name="instruments" defaultOpen>
    <File name="rigol.py" />
  </Folder>

  <Folder name="phases" defaultOpen>
    <File name="set_voltage.py" />
  </Folder>
</Files>

We'll define the plug, implement it, and use it in a phase:

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>

    <TabsTrigger value="rigol.py">
      <IconBrandPython className="size-4" />

      rigol.py
    </TabsTrigger>

    <TabsTrigger value="set_voltage.py">
      <IconBrandPython className="size-4" />

      set\_voltage.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    name: Power Supply Test

    plugs:
      - name: Power Supply
        description: Rigol DP832 programmable power supply
        python: instruments.rigol:RigolDP832

    main:
      - name: Set Voltage
        python: phases.set_voltage
    ```
  </TabsContent>

  <TabsContent value="rigol.py">
    ```python
    import socket

    class RigolDP832:
        def __init__(self):
            self.ip = "192.168.1.50"
            self.port = 5555

            self.socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
            self.socket.connect((self.ip, self.port))
            print(f"Connected to power supply at {self.ip}")

        def __del__(self):
            if hasattr(self, 'socket'):
                self.socket.close()
            print("Power supply disconnected")

        def set_voltage(self, channel, voltage):
            cmd = f":SOUR{channel}:VOLT {voltage}\n"
            self.socket.send(cmd.encode())
    ```
  </TabsContent>

  <TabsContent value="set_voltage.py">
    ```python
    def set_voltage(power_supply, measurements, log):
        power_supply.set_voltage(1, 5.0)

        measurements.voltage_set = 5.0
        log.info("Set channel 1 to 5.0V")
    ```
  </TabsContent>
</Tabs>

TofuPilot creates the plug instance when the slot starts, automatically passes it to your phase function by matching the parameter name `power_supply` to the plug key, and destroys it when the slot completes.
