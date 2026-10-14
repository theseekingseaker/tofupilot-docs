<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/phases/executable -->
# Executable
URL: /docs/frameworks/tofupilot/phases/executable

Execute shell commands, scripts, and compiled binaries as test phases to integrate external tools into your TofuPilot procedures.

***

title: Executable
description: Execute shell commands, scripts, and compiled binaries as test phases to integrate external tools into your TofuPilot procedures.
icon: SquareTerminal
--------------------

import { Callout } from 'fumadocs-ui/components/callout';
import { Tabs, TabsList, TabsTrigger, TabsContent } from 'fumadocs-ui/components/tabs';
import { IconFileCode } from '@tabler/icons-react';
import { InlineOutcomeBadge } from '@/components/inline-outcome-badge';

## Overview \[toc]

You can execute shell commands and binaries by specifying a `command`.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    main:
      - name: Flash Firmware
        executable: # [!code ++]
          command: "esptool write_flash 0x0 firmware.bin" # [!code ++]
    ```
  </TabsContent>
</Tabs>

TofuPilot runs commands through `sh` on Unix/Linux/macOS or `powershell` on Windows by default.

## Shell

You can specify which `shell` interprets your command.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    main:
      - name: Check System
        executable:
          command: "uname -a"
          shell: bash # [!code ++]

      - name: Get Windows Info
        executable:
          command: "Get-ComputerInfo"
          shell: powershell # [!code ++]

      - name: Legacy Windows Command
        executable:
          command: "dir C:\\"
          shell: cmd # [!code ++]
    ```
  </TabsContent>
</Tabs>

TofuPilot supports `bash`, `sh`, `zsh`, `powershell`, `pwsh` (PowerShell Core), and `cmd`.

## Working Directory

You can set the `working_directory` where your command is executed.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    main:
      - name: Build Project
        executable:
          command: "make build"
          working_directory: "./home/user/firmware" # [!code ++]

      - name: Run Tests
        executable:
          command: "pytest tests/"
          working_directory: "subproject" # [!code ++]
    ```
  </TabsContent>
</Tabs>

TofuPilot resolves relative paths from the procedure directory. If not specified, commands execute from the procedure directory by default.

## Exit Codes

TofuPilot marks the phase as <InlineOutcomeBadge outcome="pass" /> when the exit code is `0`, and <InlineOutcomeBadge outcome="fail" /> for any non-zero exit code.
