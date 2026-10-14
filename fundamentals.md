<!-- Source: https://www.tofupilot.com/docs/fundamentals -->
# TofuPilot fundamental concepts
URL: /docs/fundamentals

The three concepts TofuPilot is built on: the test lifecycle, the data model linking runs and measurements, and the units that trace each run to hardware.

***

title: TofuPilot fundamental concepts
sidebarTitle: Fundamental Concepts
description: "The three concepts TofuPilot is built on: the test lifecycle, the data model linking runs and measurements, and the units that trace each run to hardware."
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Cards, Card } from 'fumadocs-ui/components/card';
import { IterationCw, Check, ScanQrCode } from 'lucide-react';

The articles below explain the core concepts that shape how TofuPilot works:

<Cards>
  <Card icon={<IterationCw />} title="Test Lifecycle" href="/docs/fundamentals/lifecycle" description="Follow test data from procedure definition through deployment, operator execution, and dashboard analytics." />

  <Card icon={<Check />} title="Test Data Model" href="/docs/fundamentals/test-data-model" description="Explore how procedures, runs, phases, measurements, logs, and attachments link into one queryable graph." />

  <Card icon={<ScanQrCode />} title="Device Data Model" href="/docs/fundamentals/device-data-model" description="Explore how units, parts, revisions, batches, and sub-units link every run back to a physical unit." />
</Cards>
