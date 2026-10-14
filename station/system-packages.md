<!-- Source: https://www.tofupilot.com/docs/station/system-packages -->
# System packages
URL: /docs/station/system-packages

Let a TofuPilot station's deployment venvs import Python packages installed on the machine outside pip, so procedures can use native dependencies that pip cannot install.

***

title: System packages
description: "Let a TofuPilot station's deployment venvs import Python packages installed on the machine outside pip, so procedures can use native dependencies that pip cannot install."
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Callout } from 'fumadocs-ui/components/callout';

Each [deployment](/docs/deployments) runs in its own virtual environment that the station builds with [uv](https://docs.astral.sh/uv/) on every pull. By default that venv is isolated: it sees only the packages resolved from the deployment's lockfile. When **System packages** is on, the station builds the venv with access to the machine's system Python packages as well, so a procedure can import dependencies that are already installed on the bench but are not installable with pip.

This is the setting to use when your code relies on a native or vendor package that ships with the machine, an instrument driver, a hardware SDK, or a system library binding, rather than a wheel on an index.

## Toggle the setting

Open **Stations → *Station name* → Setup** and flip **System packages**. The dashboard pushes the new value to the bench over the live channel, and the CLI stores it locally.

The `system_packages` station config key is off by default, so existing stations keep isolated venvs until you opt in.

<Callout type="info">
  The setting applies on the **next deploy**. The station rebuilds the venv from scratch on every pull, so an already-pulled deployment keeps its current venv until you deploy or pull again. Run [`tofupilot deploy`](/docs/cli/deploy) followed by a pull on the station to provision a venv with system access.
</Callout>

## How it works

When **System packages** is on, the station adds uv's [`--system-site-packages`](https://docs.astral.sh/uv/pip/environments/) flag when it creates the deployment's venv:

```bash title="terminal"
uv venv --python 3.12 --system-site-packages venv
```

The resulting venv resolves imports from the deployment's locked dependencies first and falls back to the machine's system `site-packages` for anything not found there. Locked dependencies still take precedence, so a deployment that pins a package gets its pinned version rather than the system copy.

## Installing a system package

Install the native dependency into the machine's system Python before deploying. For example, on a Linux bench:

```bash title="terminal"
sudo apt install python3-yourdriver
```

Then enable **System packages** for the station and deploy. The next venv the station builds will see `yourdriver` without it being in the deployment's lockfile.

<Callout type="warn">
  System packages trade isolation for reach. A deployment with this setting on depends on the machine being provisioned with the right native packages, so the same deployment is no longer self-contained across stations. Keep it off unless a procedure genuinely needs a dependency that pip cannot install.
</Callout>
