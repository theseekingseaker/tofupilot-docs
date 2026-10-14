<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/monorepos -->
# Monorepos
URL: /docs/frameworks/tofupilot/monorepos

Learn how to organize one or many TofuPilot procedures in the same repository with shared phases and plugs.

***

title: Monorepos
description: "Learn how to organize one or many TofuPilot procedures in the same repository with shared phases and plugs."
icon: FolderTree
----------------

import { File, Folder, Files } from 'fumadocs-ui/components/files';

A TofuPilot project holds one procedure or many, so a monorepo is the right layout when you want to share instrument drivers, calibration sequences, and finalizer phases across procedures. The patterns below scale from a single procedure on a laptop up to a fleet of related products that ship from the same repo.

## Single procedure

When a project covers a single procedure, everything lives next to one procedure `.yaml` file. Phases, plugs, and the environment all sit in the same folder so you do not have to think about paths.

<Files>
  <Folder name="laptop-battery-functional" defaultOpen>
    <File name=".env.local" />

    <File name="procedure.yaml" />

    <Folder name="phases" defaultOpen>
      <File name="test_cell_voltage.py" />

      <File name="measure_capacity.py" />
    </Folder>

    <Folder name="plugs" defaultOpen>
      <File name="Keysight34461A.py" />

      <File name="ArbinBatteryTester.py" />
    </Folder>
  </Folder>
</Files>

## Multiple procedures

When a second product joins the repo, lift the reusable code into `shared/` and keep each procedure under `procedures/<name>/` with its own `.yaml` file. Each procedure then references shared code by relative path, so you keep one canonical copy of every driver and helper.

<Files>
  <Folder name="tofu" defaultOpen>
    <Folder name="shared" defaultOpen>
      <Folder name="plugs">
        <File name="Keysight34461A.py" />

        <File name="RigolDP832.py" />

        <File name="FlirThermalCamera.py" />
      </Folder>

      <Folder name="phases">
        <File name="initialize_instruments.py" />

        <File name="save_to_mes.py" />

        <Folder name="instrument_calibration">
          <File name="prepare_dmm.py" />

          <File name="calibrate_dmm.py" />

          <File name="verify_dmm.py" />
        </Folder>

        <Folder name="safety_checks">
          <File name="check_interlock.py" />

          <File name="verify_grounding.py" />
        </Folder>
      </Folder>
    </Folder>

    <Folder name="procedures" defaultOpen>
      <Folder name="laptop-battery-functional" defaultOpen>
        <File name=".env.local" />

        <File name="procedure.yaml" />

        <Folder name="phases">
          <File name="test_cell_voltage.py" />

          <File name="measure_capacity.py" />
        </Folder>

        <Folder name="plugs">
          <File name="ArbinBatteryTester.py" />
        </Folder>
      </Folder>

      <Folder name="laptop-battery-burnin">
        <File name=".env.local" />

        <File name="procedure.yaml" />

        <Folder name="phases">
          <File name="thermal_stress.py" />
        </Folder>
      </Folder>

      <Folder name="smartphone-display-eol">
        <File name=".env.local" />

        <File name="procedure.yaml" />

        <Folder name="phases">
          <File name="measure_brightness.py" />

          <File name="check_dead_pixels.py" />
        </Folder>
      </Folder>
    </Folder>
  </Folder>
</Files>

Each procedure `.yaml` file resolves Python references against its own directory, so shared modules are reached either via relative paths (`../../shared/phases/save_to_mes`) or by adding `shared/` to `PYTHONPATH` from `.env.local`.

## Sharing a plug across procedures

Define each plug once under `shared/plugs/` and reference it from every procedure `.yaml` file that needs it, so a single file holds the canonical driver.

```yaml title="procedures/laptop-battery-functional/procedure.yaml"
name: Laptop Battery Functional

plugs:
  - name: Power Supply
    python: ../../shared/plugs/RigolDP832:RigolDP832

main:
  - name: Test Cell Voltage
    python: phases.test_cell_voltage
```

When the driver changes, every procedure picks up the new version on the next run, so you do not have to remember to bump versions across the repo.

## Deploying each procedure from the same repo

Each TofuPilot procedure maps to its own subdirectory through two settings, set once per procedure with [`tofupilot procedures update`](/docs/cli/procedures#update) or from the procedure's settings page:

* **Root directory**: the path within the repo to the directory holding the procedure's `pyproject.toml` and `procedure.yaml`. For the layout above: `procedures/laptop-battery-functional`.
* **Entry point**: which file to run inside that directory, when the default does not fit. A `.yaml` entry point is the only way to run a procedure file with a custom name (for example `battery-functional.yaml` instead of `procedure.yaml`); a `.py` entry point points OpenHTF procedures at a file other than `main.py`.

```bash title="map each procedure to its directory"
tofupilot procedures update --id <functional-id> --root-directory procedures/laptop-battery-functional
tofupilot procedures update --id <burnin-id> --root-directory procedures/laptop-battery-burnin
```

With [Git deployments](/docs/deployments/git), one push then builds every procedure from its own directory, with no per-procedure checkouts and no renaming.
