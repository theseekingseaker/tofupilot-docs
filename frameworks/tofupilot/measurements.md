<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/measurements -->
# Measurements
URL: /docs/frameworks/tofupilot/measurements

Capture and validate structured measurement results within your test phases. Define limits, units, and validators to catch failures automatically.

***

title: Measurements
description: Capture and validate structured measurement results within your test phases. Define limits, units, and validators to catch failures automatically.
icon: DraftingCompass
---------------------

import { Tabs, Tab, TabsList, TabsTrigger, TabsContent } from 'fumadocs-ui/components/tabs';
import { Callout } from 'fumadocs-ui/components/callout';
import { IconFileCode, IconBrandPython } from '@tabler/icons-react';
import { Cards, Card } from 'fumadocs-ui/components/card';
import { Hash, Type, ToggleLeft, LineChart, Braces } from 'lucide-react';
import { MeasurementsOverviewPreview } from '@/components/MeasurementsOverviewPreview';
import { InlineOutcomeBadge } from '@/components/inline-outcome-badge';

## Overview \[toc]

<MeasurementsOverviewPreview />

<div className="sr-only" aria-hidden="false">
  ```
  Measurements Overview (one phase, three measurements)
    Device Name      | String  | "Device-A" | x != ""        | PASS
    Output Voltage   | Numeric | 5.00 V     | 4.8 < x < 5.2  | PASS
    Power Enabled    | Boolean | True       | x == False     | FAIL
  ```
</div>

You can capture numeric, string, boolean, JSON, and multi-dimensional measurements with validators and aggregations.

TofuPilot will evaluate validators, record outcomes, and timestamp each measurement.

## Type

<Cards>
  <Card icon={<Hash />} href="/docs/frameworks/tofupilot/measurements/numeric" title="Numeric">
    Floating-point values with validators and aggregations
  </Card>

  <Card icon={<Type />} href="/docs/frameworks/tofupilot/measurements/string" title="String">
    Text values with pattern matching and validation
  </Card>

  <Card icon={<ToggleLeft />} href="/docs/frameworks/tofupilot/measurements/boolean" title="Boolean">
    True/false values for binary state checks
  </Card>

  <Card icon={<Braces />} href="/docs/frameworks/tofupilot/measurements/json" title="JSON">
    Structured objects and arrays for rich device state
  </Card>

  <Card icon={<LineChart />} href="/docs/frameworks/tofupilot/measurements/multi-dimensional" title="Multi-Dimensional">
    Complex data structures for charts and waveforms
  </Card>
</Cards>

## Name

You can create a new measurement by adding it to the phase's `measurements` list.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    name: Power Supply Test

    main:
      - name: Measure Voltage
        measurements: # [!code ++]
          - name: Output Voltage # [!code ++]
    ```
  </TabsContent>
</Tabs>

TofuPilot will automatically trim whitespace and enforce a 100 character limit.

## Description

You can add an optional description to explain what the measurement captures.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    measurements:
      - name: Output Voltage
        description: "5V power rail output voltage" # [!code ++]
    ```
  </TabsContent>
</Tabs>

TofuPilot will enforce a 50,000 character limit on descriptions.

## Key

You can define a key for referencing this measurement in your Python phases or YAML procedure.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    measurements:
      - name: Output Voltage
        key: output_voltage # [!code ++]
    ```
  </TabsContent>
</Tabs>

If not specified, TofuPilot auto-generates a key from the measurement name. You can use the key in [Python phases](#value) and for [binding to operator UI](/docs/frameworks/tofupilot/operator-ui#bind-to-measurements). Keys must be valid Python identifiers: start with a letter or underscore, followed by letters, numbers, or underscores.

Your phase must record the measurement under its key. A declared measurement the phase never records is uploaded as an empty measurement, and the phase log warns that it will be filled as empty until a value is provided in the phase. An empty measurement can't meet its validators, so it is <InlineOutcomeBadge outcome="fail" /> and fails its phase. Without validators, it is <InlineOutcomeBadge outcome="unset" />. The same rule applies to the `key` of each y axis in a [multi-dimensional measurement](/docs/frameworks/tofupilot/measurements/multi-dimensional) and to the `type` of each [aggregation](#aggregations). TofuPilot Studio shows the same warning before the run when the phase's Python never writes the measurement key.

A measurement recorded as `None` is empty too, and fails its validators the same way. Only a phase that ran to its end is completed this way: a phase that was skipped, raised an error, or returned early with `phase.retry()`, `phase.stop()` or `phase.fail()` keeps only what it recorded.

A measurement recorded under a key that isn't declared is uploaded as recorded, with the validators set from Python. When a declared measurement is missing, the phase log also lists the undeclared ones, since the two are usually a naming mismatch.

## Value

You can set measurement values using the measurement name as an attribute on `measurements`.

<Tabs defaultValue="measure_voltage.py">
  <TabsList>
    <TabsTrigger value="measure_voltage.py">
      <IconBrandPython className="size-4" />

      measure\_voltage.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="measure_voltage.py">
    ```python
    def measure_voltage(run, measurements, multimeter):
        voltage = multimeter.read_voltage()
        measurements.output_voltage = voltage # [!code ++]
    ```
  </TabsContent>
</Tabs>

TofuPilot will automatically detect the measurement type from the value.

## Validators

You can validate measurements against expected ranges or patterns.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    measurements:
      - name: Output Voltage
        validators: # [!code ++]
          - operator: ">=" # [!code ++]
            expected_value: 4.8 # [!code ++]
          - operator: "<=" # [!code ++]
            expected_value: 5.2 # [!code ++]
    ```
  </TabsContent>
</Tabs>

TofuPilot will evaluate each validator and set the measurement outcome. See type-specific pages for available operators.

## Outcome

You can check measurement outcomes to determine if validators passed or failed.

TofuPilot computes the outcome from every validator on the measurement, on its [aggregations](#aggregations) and on the axes of a [multi-dimensional measurement](/docs/frameworks/tofupilot/measurements/multi-dimensional).

| Validators                                                                                           | Outcome |
| ---------------------------------------------------------------------------------------------------- | ------- |
| Any validator fails                                                                                  | `FAIL`  |
| A validator has no value to check: the measurement, the aggregation or the axis is empty             | `FAIL`  |
| None, or aggregations without validators                                                             | `UNSET` |
| A validator couldn't run, such as a numeric limit on a string                                        | `UNSET` |
| A declared axis or aggregation without validators was never recorded, even if every validator passed | `UNSET` |
| Every validator passed                                                                               | `PASS`  |

A `FAIL` wins over everything else. `UNSET` means the measurement was not fully checked.

An `UNSET` outcome does not affect the final phase result.

## Aggregations

You can compute statistics across numeric arrays.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    measurements:
      - name: Voltage Readings
        aggregations: # [!code ++]
          - type: mean # [!code ++]
            validators: # [!code ++]
              - operator: ">=" # [!code ++]
                expected_value: 4.9 # [!code ++]
    ```
  </TabsContent>
</Tabs>

Your Python phase computes and sets the aggregation values; TofuPilot validates them against the declared validators. Learn more in [Aggregations](/docs/frameworks/tofupilot/measurements/aggregations).
