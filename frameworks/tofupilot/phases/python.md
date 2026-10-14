<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/phases/python -->
# Python
URL: /docs/frameworks/tofupilot/phases/python

Run Python functions as TofuPilot test phases with full access to measurements, attachments, logging, and operator UI controls.

***

title: Python
description: Run Python functions as TofuPilot test phases with full access to measurements, attachments, logging, and operator UI controls.
icon: Code
----------

import { Callout } from 'fumadocs-ui/components/callout';
import { Tabs, TabsList, TabsTrigger, TabsContent } from 'fumadocs-ui/components/tabs';
import { File, Folder, Files } from 'fumadocs-ui/components/files';
import { IconFileCode, IconBrandPython } from '@tabler/icons-react';
import { Accordion, Accordions } from 'fumadocs-ui/components/accordion';
import { Cards, Card } from 'fumadocs-ui/components/card';
import { Check, Logs, Ruler, Variable, ListChecks, ScanQrCode, User, Plug, Paperclip, SquareMousePointer, CircleArrowRight } from 'lucide-react';

## Overview \[toc]

You can run any Python function using the `python` parameter.

Let's start by creating a Python file in a new `phases/` directory:

<Files>
  <File name="procedure.yaml" />

  <Folder name="phases" defaultOpen>
    <File name="check_device.py" />
  </Folder>
</Files>

Then add a new phase referencing this module:

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>

    <TabsTrigger value="check_device.py">
      <IconBrandPython className="size-4" />

      check\_device.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    main:
      - name: Test Functionality
        python: phases.check_device # [!code ++]
    ```
  </TabsContent>

  <TabsContent value="check_device.py">
    ```python
    def check_device():
        pass  # Your code here
    ```
  </TabsContent>
</Tabs>

TofuPilot will manage your Python [Environment](#environment), run your function, and provide access to framework features through [Function Parameters](#function-parameters).

## File

You can specify which Python file to execute using dot notation. Paths are resolved relative to the procedure package directory (the directory a deployment's `entry_point` is relative to — for a stand-alone project, the directory containing your procedure `.yaml` file).

For example, for this file tree:

<Files>
  <Folder name="my-procedure" defaultOpen>
    <File name="procedure.yaml" />

    <Folder name="phases" defaultOpen>
      <File name="foo.py" />

      <Folder name="nested" defaultOpen>
        <File name="bar.py" />
      </Folder>
    </Folder>

    <Folder name="shared" defaultOpen>
      <File name="baz.py" />
    </Folder>
  </Folder>
</Files>

You can reference these files with:

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
      - name: Foo
        python: phases.foo # [!code ++]
      - name: Bar
        python: phases.nested.bar # [!code ++]
      - name: Baz
        python: shared.baz # [!code ++]
    ```
  </TabsContent>
</Tabs>

## Function

You can specify which function to call using colon syntax: `module:function`.

TofuPilot defaults to the last component of the path as the function name.

<Tabs defaultValue="procedure.yaml">
  <TabsList>
    <TabsTrigger value="procedure.yaml">
      <IconFileCode className="size-4" />

      procedure.yaml
    </TabsTrigger>

    <TabsTrigger value="calibrate.py">
      <IconBrandPython className="size-4" />

      calibrate.py
    </TabsTrigger>
  </TabsList>

  <TabsContent value="procedure.yaml">
    ```yaml
    main:
      - name: Calibration
        python: calibrate # Calls calibrate() function by default [!code ++]

      - name: Custom Function
        python: calibrate:run_calibration # Calls run_calibration() instead [!code ++]
    ```
  </TabsContent>

  <TabsContent value="calibrate.py">
    ```python
    def calibrate():
        pass  # Default function - called by first phase

    def run_calibration():
        pass  # Custom function - called by second phase
    ```
  </TabsContent>
</Tabs>

## Environment

### Overview

TofuPilot automatically manages Python virtual environments and runs your code in isolation, ensuring consistent behavior across development and production.

As soon as you add a Python phase to your procedure, TofuPilot automatically:

1. **Looks for an existing `venv`** directory in your procedure directory (or workspace root in a monorepo)
2. **If found:** Uses it as-is, unless your dependencies or Python version changed
3. **If not found:** Creates a `venv` directory with the specified Python version (defaults to 3.11)
4. **Installs dependencies** from `pyproject.toml` or `requirements.txt` if either exists (the venv is created without dependencies if neither is present)
5. **Re-syncs dependencies** whenever `pyproject.toml`, `requirements.txt`, or the Python version changes

### Dependencies

Edit `pyproject.toml` in your procedure directory to manage dependencies:

```toml
[project]
name = "my-procedure"
version = "0.1.0"
requires-python = ">=3.11"
dependencies = [
    "numpy>=1.24.0",
    "pandas>=2.0.0",
]
```

TofuPilot will automatically install these dependencies when you run your procedure.

Under the hood, TofuPilot uses [UV](https://docs.astral.sh/uv/), a Python package manager faster than pip, with full support for the [PEP 508](https://peps.python.org/pep-0508/) dependency specification.

### Python version

TofuPilot builds on the **lowest** supported Python that satisfies `requires-python` in your `pyproject.toml`. If you omit `requires-python`, the default is `3.12`.

```toml
[project]
requires-python = ">=3.12"
```

`>=3.12` builds on 3.12; `>=3.13` builds on 3.13. The lowest satisfying version is chosen because packages drop support for older Python far more slowly than they add it for newer, so the floor maximizes dependency compatibility.

Selection happens at the **minor** level (`3.12`), not the patch (`3.12.4`). The station provisions a virtual environment on whichever compatible patch is available or fetchable, and the dependency wheels are compatible across patches of the same minor line.

Supported minor versions:

| Version | Notes                                     |
| ------- | ----------------------------------------- |
| 3.10    | Oldest supported                          |
| 3.11    |                                           |
| 3.12    | Default when `requires-python` is omitted |
| 3.13    |                                           |
| 3.14    | Newest supported                          |

<Callout type="warn">
  Use a range, not an exact pin. An exact pin like `==3.13` or `requires-python = "==3.13.4"` matches a single patch that the build may not ship, and the build fails with "no supported Python satisfies requires-python". Pin a lower bound (and optionally an upper bound) instead:

  ```toml
  [project]
  requires-python = ">=3.13,<3.14"
  ```

  If you must pin a single minor, use the wildcard form `==3.13.*` (matches any 3.13 patch), not `==3.13`.
</Callout>

For advanced use cases, you can configure private registries, local packages, Git dependencies, and platform-specific requirements directly in your `pyproject.toml`:

<Accordions>
  <Accordion title="Private packages from internal registries">
    Add your private package registry to `pyproject.toml`:

    ```toml
    [project]
    dependencies = [
        "my-internal-package>=1.0.0",
    ]

    [[tool.uv.index]]
    name = "internal"
    url = "https://pypi.your-company.com/simple"
    ```

    For authentication, create a `.netrc` file in your home directory:

    ```
    machine pypi.your-company.com
    login your-username
    password your-token
    ```

    TofuPilot will automatically use your configured index when syncing dependencies.
  </Accordion>

  <Accordion title="Local packages and editable installs">
    For local development packages, use `tool.uv.sources`:

    ```toml
    [project]
    dependencies = [
        "my-local-package",
    ]

    [tool.uv.sources]
    my-local-package = { path = "../my-local-package", editable = true }
    ```

    This installs your local package in editable mode, so changes are reflected immediately without reinstalling.
  </Accordion>

  <Accordion title="Git dependencies">
    Install packages directly from Git repositories:

    ```toml
    [project]
    dependencies = [
        "my-git-package",
    ]

    [tool.uv.sources]
    my-git-package = { git = "https://github.com/your-org/my-git-package", tag = "v1.0.0" }
    ```

    You can use `tag`, `branch`, or `rev` (commit SHA) to specify the version.
  </Accordion>

  <Accordion title="Platform-specific dependencies">
    Use environment markers to install packages only on specific platforms:

    ```toml
    [project]
    dependencies = [
        "jax; sys_platform == 'linux'",
        "numpy; python_version >= '3.11'",
        "windows-curses; sys_platform == 'win32'",
    ]
    ```

    TofuPilot will only install these packages when the platform conditions match.
  </Accordion>

  <Accordion title="Optional dependencies">
    Group optional features using extras:

    ```toml
    [project.optional-dependencies]
    plotting = ["matplotlib>=3.6.0", "seaborn>=0.12.0"]
    data = ["pandas>=2.0.0", "openpyxl>=3.1.0"]
    all = ["matplotlib>=3.6.0", "seaborn>=0.12.0", "pandas>=2.0.0", "openpyxl>=3.1.0"]
    ```

    Install specific extras by adding them to your main dependencies:

    ```toml
    [project]
    dependencies = [
        "my-package[plotting]",
    ]
    ```
  </Accordion>
</Accordions>

## Function Parameters

TofuPilot automatically injects parameters based on your function signature. You can refer to each framework feature's documentation for Python function parameters usage.

<Cards>
  <Card icon={<CircleArrowRight />} title="Phase Outcome" href="/docs/frameworks/tofupilot/phases#outcome">
    Use `phase` to set phase outcome
  </Card>

  <Card icon={<Ruler />} title="Measurements" href="/docs/frameworks/tofupilot/measurements">
    Use `measurements` to record test data
  </Card>

  <Card icon={<Logs />} title="Logs" href="/docs/frameworks/tofupilot/logs">
    Use `log` to output messages
  </Card>

  <Card icon={<SquareMousePointer />} title="Operator UI" href="/docs/frameworks/tofupilot/operator-ui">
    Use `ui` to update interface components
  </Card>

  <Card icon={<Paperclip />} title="Attachments" href="/docs/frameworks/tofupilot/attachments">
    Use `attach` to attach files and data to reports
  </Card>

  <Card icon={<Plug />} title="Plugs" href="/docs/frameworks/tofupilot/plugs">
    Use your plug methods from any phase
  </Card>

  <Card icon={<ScanQrCode />} title="Unit Under Test" href="/docs/frameworks/tofupilot/unit-under-test">
    Use `unit` to set UUT metadata
  </Card>

  <Card icon={<Check />} title="Run" href="/docs/fundamentals/test-data-model#run">
    Use `run` to access the full test context
  </Card>

  <Card icon={<CircleArrowRight />} title="Previous Results" href="/docs/frameworks/tofupilot/phases#previous-results">
    Use a completed phase's key to access its results
  </Card>
</Cards>
