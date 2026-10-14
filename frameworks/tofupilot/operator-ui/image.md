<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/operator-ui/image -->
# Image
URL: /docs/frameworks/tofupilot/operator-ui/image

Display static or dynamically updating images in the TofuPilot operator UI for visual instructions and live test feedback.

***

title: Image
description: Display static or dynamically updating images in the TofuPilot operator UI for visual instructions and live test feedback.
icon: Image
-----------

import { IsolatedUIPreview } from '@/components/IsolatedUIPreview';
import { Callout } from 'fumadocs-ui/components/callout';
import cableFfc from './images/cable-ffc.jpg';

## Preview \[toc]

<IsolatedUIPreview type="image" label="Reference PCB Image" description="Compare your unit to this reference image" value={cableFfc.src} width="80%" aspect="16/9" fit="cover" containerHeight="520px" />

<div className="sr-only" aria-hidden="false">
  ```
  Operator UI: Image (display only)
    Label: Reference PCB Image
    Help: Compare your unit to this reference image
  ```
</div>

You can create an image display with `image`:

```yaml
ui:
  components:
    - key: reference_image # [!code ++]
      type: image # [!code ++]
      label: "Reference PCB Image" # [!code ++]
      description: "Compare your unit to this reference image" # [!code ++]
      default_value: "./reference.png" # [!code ++]
      width: 80% # [!code ++]
      aspect: 16/9 # [!code ++]
```

## Properties

### Display

You can configure how the image appears to the operator.

| Property      | Type     | Required | Description                                                                                 |
| ------------- | -------- | -------- | ------------------------------------------------------------------------------------------- |
| `key`         | `string` | Yes      | Unique identifier for updating the image                                                    |
| `type`        | `string` | Yes      | Must be `"image"`                                                                           |
| `label`       | `string` | No       | Display label above the image                                                               |
| `description` | `string` | No       | Helper text displayed below the label                                                       |
| `placeholder` | `string` | No       | Text shown while image is loading                                                           |
| `width`       | `string` | No       | Container width (e.g., `50%`, `100%`, default: `100%`)                                      |
| `height`      | `string` | No       | Container height (e.g., `400px`, `800px`)                                                   |
| `aspect`      | `string` | No       | Aspect ratio: `"16/9"`, `"4/3"`, `"3/4"`, `"2/3"`, `"9/16"`, `"square"` (default), `"auto"` |
| `fit`         | `string` | No       | Fit mode: `"contain"` (default), `"cover"`, `"fill"`                                        |

### Value

You can set default values and update them dynamically from Python.

| Property        | Type     | Required | Description                                                                    |
| --------------- | -------- | -------- | ------------------------------------------------------------------------------ |
| `default_value` | `string` | No       | Image source: a path relative to the procedure directory, a URL, or a data URI |

Runtime updates from Python (`ui.<key> = ...`) accept the same formats.

TofuPilot supports raster image formats: PNG, JPEG, GIF, WebP, BMP, AVIF, and ICO. SVG is not served. It can carry inline scripts and the operator UI renders images from the same origin as the app, so an SVG would be a cross-site scripting vector. Convert vector art to PNG before referencing it.

## Examples

### Static Reference Image

We'll display a static image from a file:

```yaml
name: Enclosure Inspection

main:
  - name: Visual Inspection
    ui:
      components:
        - key: reference_image
          type: image
          label: "Reference PCB Image"
          description: "Compare your unit to this reference image"
          default_value: "./reference_pcb.png"
          width: 100%
          height: 600px
          fit: contain

        - key: inspection_result
          type: radio
          label: "Does unit match reference?"
          required: true
          options:
            - label: "Match"
              value: "pass"
            - label: "No Match"
              value: "fail"
```

### Camera Capture

We'll capture and display images from a camera:

```yaml
name: Camera Module Test

plugs:
  - key: camera
    name: Inspection Camera
    python: plugs.camera:Camera

main:
  - name: Camera Capture
    python: phases.capture_image
    ui:
      components:
        - key: captured_image
          type: image
          label: "Captured Unit Image"
          description: "Live image from inspection camera"
          width: 100%
          height: 480px
          fit: contain
          placeholder: "Capturing image..."
```

```python
import base64
import io

def capture_image(ui, log, camera):
    log.info("Capturing image from camera")

    img = camera.capture()

    buffer = io.BytesIO()
    img.save(buffer, format='PNG')
    img_base64 = base64.b64encode(buffer.getvalue()).decode('utf-8')

    ui.captured_image = f'data:image/png;base64,{img_base64}'
```
