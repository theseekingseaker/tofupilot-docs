<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/operator-ui/progress -->
# Progress
URL: /docs/frameworks/tofupilot/operator-ui/progress

Display a real-time progress bar during test execution to keep operators informed of completion status in TofuPilot.

***

title: Progress
description: Display a real-time progress bar during test execution to keep operators informed of completion status in TofuPilot.
icon: Minus
-----------

import { IsolatedUIPreview } from '@/components/IsolatedUIPreview';
import { Callout } from 'fumadocs-ui/components/callout';

## Preview \[toc]

<IsolatedUIPreview type="progress" label="Firmware Download" description="Downloading firmware to device" value={50} max={100} />

<div className="sr-only" aria-hidden="false">
  ```
  Operator UI: Progress (display only)
    Label: Firmware Download
    Value: 50 / 100
    Help: Downloading firmware to device
  ```
</div>

You can create a progress bar with `progress`:

```yaml
ui:
  components:
    - key: download_progress # [!code ++]
      type: progress # [!code ++]
      label: "Firmware Download" # [!code ++]
      description: "Downloading firmware to device" # [!code ++]
      default_value: 0 # [!code ++]
      max: 100 # [!code ++]
```

## Properties

### Display

You can configure how the progress bar appears to the operator.

| Property      | Type     | Required | Description                              |
| ------------- | -------- | -------- | ---------------------------------------- |
| `key`         | `string` | Yes      | Unique identifier for updating the value |
| `type`        | `string` | Yes      | Must be `"progress"`                     |
| `label`       | `string` | No       | Display label above the progress bar     |
| `description` | `string` | No       | Helper text displayed below the label    |

### Value

You can set default values and update them dynamically from Python.

| Property        | Type     | Required | Description                    |
| --------------- | -------- | -------- | ------------------------------ |
| `default_value` | `number` | No       | Initial progress value         |
| `min`           | `number` | No       | Minimum value (default: `0`)   |
| `max`           | `number` | No       | Maximum value (default: `100`) |

## Examples

### Firmware Download

We'll show progress while downloading firmware to the device:

```yaml
name: Controller Board Test

plugs:
  - key: device
    name: Device
    python: plugs.device:Device

main:
  - name: Firmware Update
    python: phases.update_firmware
    ui:
      components:
        - key: download_progress
          type: progress
          label: "Firmware Download"
          description: "Downloading firmware to device"
          default_value: 0
          max: 100
```

```python
import time

def update_firmware(ui, log, device):
    firmware_size = 2048  # KB
    chunk_size = 64  # KB
    downloaded = 0

    log.info("Starting firmware download")

    while downloaded < firmware_size:
        chunk = min(chunk_size, firmware_size - downloaded)
        device.write_firmware_chunk(chunk)
        downloaded += chunk

        progress = int((downloaded / firmware_size) * 100)
        ui.download_progress = progress

        time.sleep(0.1)

    log.info("Firmware download complete")
```

### Multi-Stage Progress

We'll track progress across multiple test stages:

```yaml
name: Assembly Line Test

main:
  - name: Multi-Stage Test
    python: phases.multi_stage_test
    ui:
      components:
        - key: overall_progress
          type: progress
          label: "Overall Progress"
          description: "Total progress across all stages"
          default_value: 0
          max: 100

        - key: stage_name
          type: text
          label: "Current Stage"
          default_value: ""

        - key: stage_progress
          type: progress
          label: "Stage Progress"
          description: "Progress within current stage"
          default_value: 0
          max: 100
```

```python
import time

def multi_stage_test(ui, log):
    stages = [
        ("Initialization", 10),
        ("Calibration", 20),
        ("Testing", 50),
        ("Cleanup", 10)
    ]

    total_progress = 0

    for stage_name, stage_steps in stages:
        ui.stage_name = stage_name
        log.info(f"Starting: {stage_name}")

        for step in range(stage_steps + 1):
            stage_progress = int((step / stage_steps) * 100)
            ui.stage_progress = stage_progress

            overall = total_progress + (step / stage_steps) * (stage_steps / 100)
            ui.overall_progress = int(overall)

            time.sleep(0.1)

        total_progress += stage_steps
```
