<!-- Source: https://www.tofupilot.com/docs/getting-started-with-tofupilot -->
# Getting started with TofuPilot
URL: /docs/getting-started-with-tofupilot

Deploy your first test procedure on TofuPilot in two steps: clone a ready-made template to your git provider, then deploy it to a new test station.

***

title: Getting started with TofuPilot
sidebarTitle: Getting Started
description: "Deploy your first test procedure on TofuPilot in two steps: clone a ready-made template to your git provider, then deploy it to a new test station."
------------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Step, Steps } from 'fumadocs-ui/components/steps';
import { Tabs, Tab } from 'fumadocs-ui/components/tabs';

Deploy your test procedure on TofuPilot in two steps: clone a template and deploy to a new station.

## Prerequisites

* A [TofuPilot account](https://www.tofupilot.app/signup).
* A Linux, macOS, or Windows computer with internet access.

## Deploy your first procedure

<Steps>
  <Step>
    ### Sign in

    Open [tofupilot.app](https://www.tofupilot.app) and sign in for free.
  </Step>

  <Step>
    ### Create procedure

    Click **New Procedure**, connect your Git provider, and clone a starter template. The wizard creates a procedure and a station, then ships a first deployment to it.
  </Step>

  <Step>
    ### Run on station

    Run the install command on the station (or your own computer to start). The CLI signs in, pulls the deployment, and runs the test on the operator UI page and the local kiosk UI.
  </Step>
</Steps>

## Explore the dashboard

After a few runs, open the dashboard to monitor your tests.

* [Runs](/docs/fundamentals/test-data-model#run): See full test reports with phases, measurements, logs, and attachments.
* [Units](/docs/inventory/units): See the full history of every unit by serial number.
* [Analytics](/docs/run-analytics): Track yield, durations, and process control across procedures and stations.

## Edit the procedure

<Steps>
  <Step>
    ### Clone the repo

    The template repo has been cloned to your GitHub organization. Clone it locally and edit it based on your needs, adding [phases](/docs/fundamentals/test-data-model#phase), [measurements](/docs/fundamentals/test-data-model#measurement), [UI components](/docs/frameworks/tofupilot/operator-ui), and more.
  </Step>

  <Step>
    ### Set up the CLI

    If not installed yet:

    <Tabs items={['macOS / Linux', 'Windows']}>
      <Tab value="macOS / Linux">
        ```bash title="terminal"
        curl -fsSL https://www.tofupilot.app/install | sh
        ```
      </Tab>

      <Tab value="Windows">
        ```powershell title="terminal"
        $p = "$env:TEMP\tp-install.ps1"; irm https://www.tofupilot.app/install.ps1 -OutFile $p; powershell -ExecutionPolicy Bypass -File $p; ri $p -EA 0
        ```
      </Tab>
    </Tabs>

    If already installed in station mode:

    ```bash title="terminal"
    tofupilot logout
    ```
  </Step>

  <Step>
    ### Run locally

    ```bash title="terminal"
    tofupilot login
    tofupilot run
    ```

    Every push to the production branch (defaults to `main`) auto-deploys to linked stations.
  </Step>
</Steps>
