<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/attachments -->
# Attachments
URL: /docs/frameworks/tofupilot/attachments

Attach images, PDFs, CSV files, and binary data to test reports during execution for traceability and failure analysis in TofuPilot.

***

title: Attachments
description: Attach images, PDFs, CSV files, and binary data to test reports during execution for traceability and failure analysis in TofuPilot.
icon: Paperclip
---------------

import { Tabs, TabsList, TabsTrigger, TabsContent } from 'fumadocs-ui/components/tabs';
import { IconBrandPython } from '@tabler/icons-react';

## Overview \[toc]

You can attach files or data to test reports during execution.

TofuPilot automatically stores attachments with test results and uploads them to your Dashboard when configured.

## File

You can use `attach.file()` to attach an existing file from the filesystem.

<Tabs defaultValue="test_device.py">
  <TabsList>
    <TabsTrigger value="test_device.py">
      <IconBrandPython className="size-4" />

      test\_device.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="test_device.py">
    ```python
    def test_device(attach, camera):
        photo_path = camera.take_photo()
        attach.file(photo_path) # [!code ++]
    ```
  </TabsContent>
</Tabs>

You can rename the attachment by providing a second parameter.

<Tabs defaultValue="test_device.py">
  <TabsList>
    <TabsTrigger value="test_device.py">
      <IconBrandPython className="size-4" />

      test\_device.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="test_device.py">
    ```python
    def test_device(attach, camera):
        photo_path = camera.take_photo()
        attach.file(photo_path, "device_photo.jpg") # [!code ++]
    ```
  </TabsContent>
</Tabs>

## Data

You can use `attach.data()` to attach binary data directly without writing to disk first.

<Tabs defaultValue="test_device.py">
  <TabsList>
    <TabsTrigger value="test_device.py">
      <IconBrandPython className="size-4" />

      test\_device.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="test_device.py">
    ```python
    import json

    def test_device(attach, device):
        diagnostics = device.run_diagnostics()
        data = json.dumps(diagnostics, indent=2).encode()
        attach.data(data, "diagnostics.json") # [!code ++]
    ```
  </TabsContent>
</Tabs>

## Where image attachments appear

Image attachments (PNG, JPEG, GIF, WebP, BMP, AVIF, ICO) render inline in the operator UI. Other file types show as a labeled row.

The local station kiosk renders an image attachment the moment you attach it, served from a local scratch copy while the run is live. Once the run uploads to the Dashboard the local copy is removed, so the kiosk shows the image during the run, not after.

The Dashboard renders an image attachment after its upload completes, and keeps it with the run for later review. A run viewed remotely while it executes shows each image as it finishes uploading.
