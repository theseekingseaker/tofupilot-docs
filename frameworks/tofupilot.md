<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot -->
# TofuPilot Framework
URL: /docs/frameworks/tofupilot

Learn how to author a TofuPilot procedure in a single YAML file and run it with the CLI, with native support for phases, measurements, plugs, and operator UI.

***

title: TofuPilot Framework
description: "Learn how to author a TofuPilot procedure in a single YAML file and run it with the CLI, with native support for phases, measurements, plugs, and operator UI."
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Tab, Tabs } from 'fumadocs-ui/components/tabs';
import { File, Folder, Files } from 'fumadocs-ui/components/files';
import { Step, Steps } from 'fumadocs-ui/components/steps';

The TofuPilot Framework is an open-source test framework purpose-built for hardware. It brings:

* **[Rich measurements](/docs/frameworks/tofupilot/measurements):** Capture typed values, waveforms, and aggregations with validation
* **[Operator UI](/docs/frameworks/tofupilot/operator-ui):** Create interactive operator UIs with no frontend development required
* **[Unit traceability](/docs/frameworks/tofupilot/unit-under-test):** Identify each unit under test by serial number and track its metadata across runs
* **[Parallel execution](/docs/frameworks/tofupilot/execution):** Run multiple phases and units simultaneously to reduce cycle time
* **[Equipment drivers](/docs/frameworks/tofupilot/plugs):** Reusable plugs to connect any test equipment or external resource
* **[Cross-platform support](/docs/frameworks/tofupilot/environments):** Build once, deploy on Windows, Linux or macOS

A central [procedure](/docs/frameworks/tofupilot/procedures) `.yaml` file defines the sequence. [Phases](/docs/frameworks/tofupilot/phases) run any logic you write as simple Python functions, and [plugs](/docs/frameworks/tofupilot/plugs) connect any instrument or resource as Python classes.

Procedures can be run in [development](/docs/frameworks/tofupilot/environments/development) or [station](/docs/frameworks/tofupilot/environments/station) mode using the TofuPilot CLI, a lightweight executable available for all major operating systems and architectures. On stations, the CLI streams tests in real time to the dashboard, so you can [control runs remotely](/docs/station/remote-ui) and [deploy](/docs/deployments) procedure updates on a push. When the network drops, runs [queue offline](/docs/station/offline-upload) and sync once it returns.

The [framework](https://github.com/tofupilot/framework) is open-source under the MIT license. The CLI is a free download for macOS, Linux and Windows, and `tofupilot run ./procedure.yaml` executes a procedure on your machine without an account. Both are built on a Rust engine that runs phases and slots in parallel, while everything you write stays plain Python.

## Getting started

### Deploy a template to a station

Start from a template, deploy it, then adapt the code.

<Steps>
  <Step>
    Connect or create a free account on the [dashboard](https://tofupilot.app).
  </Step>

  <Step>
    Follow the new-procedure assistant: pick a starter template, create a [station](/docs/frameworks/tofupilot/environments/station), and deploy.
  </Step>

  <Step>
    Run the test on the station.
  </Step>

  <Step>
    Clone the repository, adapt the phases and plugs, and push to redeploy.
  </Step>
</Steps>

<TemplateCards filter={['tofupilot/template-framework-starter', 'tofupilot/template-framework-measurements', 'tofupilot/template-framework-operator-ui', 'tofupilot/template-framework-plugs']} />

### Start from scratch

Build it yourself, locally first.

<Steps>
  <Step>
    Install the CLI.

    <Tabs items={['Linux', 'macOS', 'Windows']}>
      <Tab value="Linux">
        ```bash
        curl -fsSL https://www.tofupilot.app/install | sh
        ```
      </Tab>

      <Tab value="macOS">
        ```bash
        curl -fsSL https://www.tofupilot.app/install | sh
        ```
      </Tab>

      <Tab value="Windows">
        ```powershell
        $p = "$env:TEMP\tp-install.ps1"; irm https://www.tofupilot.app/install.ps1 -OutFile $p; powershell -ExecutionPolicy Bypass -File $p; ri $p -EA 0
        ```
      </Tab>
    </Tabs>
  </Step>

  <Step>
    Write a procedure `.yaml` file with `phases/` and `plugs/` (see the [example](#example-procedure) below).
  </Step>

  <Step>
    Run it locally with `tofupilot run ./battery-test`.
  </Step>

  <Step>
    [Log in](/docs/cli/login), [link](/docs/cli/link) the procedure to your dashboard, and re-run with `--upload` to sync results. Then deploy it to a station.
  </Step>
</Steps>

## Example procedure

A procedure is a `.yaml` file that declares phases and plugs, plus the Python they reference. This one reads a voltage from a power supply and validates it:

<Files>
  <Folder name="battery-test" defaultOpen>
    <File name="procedure.yaml" />

    <Folder name="phases" defaultOpen>
      <File name="measure_voltage.py" />
    </Folder>

    <Folder name="plugs" defaultOpen>
      <File name="psu.py" />
    </Folder>
  </Folder>
</Files>

<Tabs items={['procedure.yaml', 'phases/measure_voltage.py', 'plugs/psu.py']}>
  <Tab value="procedure.yaml">
    ```yaml
    name: Battery Functional Test

    unit:
      serial_number:
        default_value: "BAT000001"
      part_number:
        default_value: "BAT-001"

    plugs:
      - name: power_supply
        python: plugs.psu:PowerSupply

    main:
      - name: Measure Voltage
        python: phases.measure_voltage
        measurements:
          - name: voltage
            unit: V
            validators:
              - operator: ">="
                expected_value: 4.8
              - operator: "<="
                expected_value: 5.2
    ```
  </Tab>

  <Tab value="phases/measure_voltage.py">
    ```python
    def measure_voltage(measurements, power_supply):
        measurements.voltage = power_supply.read_voltage()
    ```
  </Tab>

  <Tab value="plugs/psu.py">
    ```python
    class PowerSupply:
        def read_voltage(self) -> float:
            return 5.03
    ```
  </Tab>
</Tabs>

Run it from the procedure directory with `tofupilot run`.
