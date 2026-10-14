<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/procedures -->
# Procedures
URL: /docs/frameworks/tofupilot/procedures

Define complete hardware test workflows with phases, measurements, and configurations. Structure reusable test procedures with TofuPilot Framework.

***

title: Procedures
description: Define complete hardware test workflows with phases, measurements, and configurations. Structure reusable test procedures with TofuPilot Framework.
icon: FileCode
--------------

import { Tabs, TabsList, TabsTrigger, TabsContent } from 'fumadocs-ui/components/tabs';
import { IconFileCode, IconBrandPython } from '@tabler/icons-react';
import { File, Folder, Files } from 'fumadocs-ui/components/files';

A procedure is a YAML file that defines your complete test sequence: what to test, how to test it, and what measurements to collect.

## File

You can use any name for your procedure file as long as it ends in `.yaml` or `.yml`.

<Files>
  <Folder name="my-test" defaultOpen>
    <File name="procedure.yaml" />
  </Folder>
</Files>

The file name doesn't affect functionality.

## Name

You must set a name for your test procedure.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    name: Battery Functional Test # [!code ++]
    ```
  </TabsContent>
</Tabs>

TofuPilot uses this name to identify the procedure in test reports and during execution. The dashboard procedure name is separate, since [linking](/docs/cli/link) binds by id.

## Version

You can optionally set a version for your procedure, recorded with every run so you can filter and compare results by it. Semantic versioning (`1.0.0`) is recommended.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    name: Battery Functional Test
    version: 1.0.0 # [!code ++]
    ```
  </TabsContent>
</Tabs>

The version is a label you set, unlike a [deployment](/docs/deployments), which is created for every commit and records the exact code that ran. Several deployments can share the same version.

## Description

You can add an optional description to document what your procedure tests.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    name: Battery Functional Test
    version: 1.0.0
    description: Validates battery voltage, capacity, and charge cycles # [!code ++]
    ```
  </TabsContent>
</Tabs>

TofuPilot displays this description in test reports and the procedure overview.

## Run Metadata

You can attach custom key-value metadata to a test run from any phase function by adding `run` as a parameter.

<Tabs defaultValue="set_metadata.py">
  <TabsList>
    <TabsTrigger value="set_metadata.py">
      <IconBrandPython className="size-4" />

      set\_metadata.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="set_metadata.py">
    ```python
    def set_metadata(run):
        run.metadata["line"] = "L3" # [!code ++]
        run.metadata["fixture_rev"] = 7 # [!code ++]
        run.metadata["ambient_temp_c"] = 23.4 # [!code ++]
    ```
  </TabsContent>
</Tabs>

TofuPilot stores run metadata in test reports and syncs it to your Dashboard, where you can filter runs by any key.

Values can be strings, numbers, or booleans (strings up to 50000 characters). Keys are limited to 40 characters (letters, digits, and `_` `.` `:` `+` `-`), with at most 50 keys per run. An assignment with an invalid key or value raises an error at the faulty line and fails the phase. Setting an existing key overwrites its value.

Each phase validates its own writes; if several phases collectively set more than 50 distinct keys, the upload keeps the first 50 in alphabetical order and logs a warning for the dropped keys.

To store metadata on the unit under test instead of the run, see [Unit Metadata](/docs/frameworks/tofupilot/unit-under-test#metadata).
