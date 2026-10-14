<!-- Source: https://www.tofupilot.com/docs/frameworks/tofupilot/environments -->
# Environments
URL: /docs/frameworks/tofupilot/environments

Choose between Studio, Station, and CLI environments to develop, debug, and deploy your TofuPilot test procedures.

***

title: Environments
description: Choose between Studio, Station, and CLI environments to develop, debug, and deploy your TofuPilot test procedures.
icon: Play
----------

import { Card, Cards } from 'fumadocs-ui/components/card';
import { PencilRuler, Computer, TowerControl } from 'lucide-react';

TofuPilot provides multiple environments for running test procedures, from development to production deployment.

You can use TofuPilot Studio for development and debugging with real-time monitoring and operator UI preview.

<Card icon={<PencilRuler />} href="/docs/frameworks/tofupilot/environments/development" title="Development">
  For development.
</Card>

You can use TofuPilot Station for production testing with simplified operator interface and automatic report sync.

<Card icon={<Computer />} href="/docs/frameworks/tofupilot/environments/station" title="TofuPilot Station">
  For production.
</Card>

TofuPilot Studio and Station include native synchronization to TofuPilot Dashboard, available in cloud and self-hosted.

<Card icon={<TowerControl />} href="/docs" title="TofuPilot Dashboard">
  View and analyze test results in the cloud.
</Card>
