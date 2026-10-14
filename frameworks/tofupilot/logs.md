<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/logs -->
# Logs
URL: /docs/frameworks/tofupilot/logs

Log structured messages during test execution for debugging, traceability, and post-run analysis of TofuPilot test procedures.

***

title: Logs
description: Log structured messages during test execution for debugging, traceability, and post-run analysis of TofuPilot test procedures.
icon: Logs
----------

import { Callout } from 'fumadocs-ui/components/callout';
import { Tabs, TabsList, TabsTrigger, TabsContent } from 'fumadocs-ui/components/tabs';
import { IconBrandPython } from '@tabler/icons-react';

## Overview \[toc]

You can record messages during test execution for debugging and traceability.

TofuPilot automatically captures the level, timestamp, line number, and file name for each log entry and records it in the test report.

## Debug

You can use `log.debug()` to log diagnostic information.

<Tabs defaultValue="measure_voltage.py">
  <TabsList>
    <TabsTrigger value="measure_voltage.py">
      <IconBrandPython className="size-4" />

      measure\_voltage.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="measure_voltage.py">
    ```python
    def measure_voltage(log, measurements, multimeter):
        log.debug(f"Multimeter address: {multimeter.address}") # [!code ++]
        voltage = multimeter.read_voltage()
        measurements.voltage = voltage
    ```
  </TabsContent>
</Tabs>

You can use debug logs to record low-level details like device addresses, raw data, or internal state that help troubleshoot issues during development.

## Info

You can use `log.info()` to log informational messages, or use `print()`.

<Tabs defaultValue="measure_voltage.py">
  <TabsList>
    <TabsTrigger value="measure_voltage.py">
      <IconBrandPython className="size-4" />

      measure\_voltage.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="measure_voltage.py">
    ```python
    def measure_voltage(log, measurements, multimeter):
        log.info("Starting voltage measurement") # [!code ++]
        voltage = multimeter.read_voltage()
        measurements.voltage = voltage
    ```
  </TabsContent>
</Tabs>

You can use info logs to track key milestones, record operation starts, or document normal behavior.

## Warning

You can use `log.warning()` to log warnings, or use `sys.stderr.write()`.

<Tabs defaultValue="measure_voltage.py">
  <TabsList>
    <TabsTrigger value="measure_voltage.py">
      <IconBrandPython className="size-4" />

      measure\_voltage.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="measure_voltage.py">
    ```python
    def measure_voltage(log, measurements, multimeter):
        voltage = multimeter.read_voltage()
        log.warning("Voltage reading took longer than expected") # [!code ++]
        measurements.voltage = voltage
    ```
  </TabsContent>
</Tabs>

You can use warning logs to flag unexpected behavior, slow operations, or edge cases that don't cause test failure but need attention.

## Error

You can use `log.error()` to log errors. Logged errors do not affect the phase outcome, only thrown errors do.

<Tabs defaultValue="measure_voltage.py">
  <TabsList>
    <TabsTrigger value="measure_voltage.py">
      <IconBrandPython className="size-4" />

      measure\_voltage.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="measure_voltage.py">
    ```python
    def measure_voltage(phase, log, measurements, multimeter):
        if not multimeter.is_connected(): # [!code ++]
            log.error("Multimeter connection lost") # [!code ++]
            phase.fail() # [!code ++]
            return # [!code ++]

        voltage = multimeter.read_voltage()
        measurements.voltage = voltage
    ```
  </TabsContent>
</Tabs>

You can use error logs to record critical failures like connection loss, invalid responses, or conditions that cause the test to fail.
