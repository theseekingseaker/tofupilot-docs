<!-- Source: https://www.tofupilot.com/docs/fundamentals/device-data-model -->
# Device Data Model
URL: /docs/fundamentals/device-data-model

Trace every test run back to the physical hardware it came from, using the parts, revisions, batches, units, and sub-units that make up the device data model.

***

title: Device Data Model
description: "Trace every test run back to the physical hardware it came from, using the parts, revisions, batches, units, and sub-units that make up the device data model."
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------

import { DeviceDataModelDiagramLazy } from '@/components/device-data-model-diagram-lazy';
import { Cards, Card } from 'fumadocs-ui/components/card';

export const Method = ({ children }) => {
  const colors = {
    GET: 'text-green-600 dark:text-green-400',
    POST: 'text-blue-600 dark:text-blue-400',
    PUT: 'text-yellow-600 dark:text-yellow-400',
    PATCH: 'text-orange-600 dark:text-orange-400',
    DELETE: 'text-red-600 dark:text-red-400',
  };
  return <span className={`font-mono font-medium ${colors[children] || ''}`}>{children}</span>;
};

The TofuPilot Device Data Model makes every run traceable to a physical unit and the part, revision, batch, and sub-units that define it.

<DeviceDataModelDiagramLazy />

<div className="sr-only" aria-hidden="false">
  ```
  Part (e.g. PCB-V1)
  └── Revision (e.g. Rev-B, defaults to A)

  Batch (e.g. B-2026-04, optional)

  Unit (serial_number, e.g. SN-001)
  ├── Part + Revision (+ optional Batch)
  └── Sub-units (child serials: PCB, battery, enclosure, cable)
  ```
</div>

## Part

A part is the product or component family that units belong to.

### Revision

A revision is a version of a part.

## Unit

A unit is one physical unit under test, identified by a [serial number](/docs/inventory/units#identity). Each unit belongs to one part, one revision, and an optional batch.

### Sub-unit

A sub-unit is a child unit assembled into a parent unit, such as a PCB inside a finished product. A parent unit can reference one or more sub-units.

## Batch

A batch is an optional production cohort, grouping units produced in the same lot.

## Schema reference

The full schema and endpoints for every entity are documented in the [REST API reference](/docs/api/rest-api).

<Cards>
  <Card title={<>Create a unit <Method>POST</Method></>} href="/docs/api/rest-api/units/create" description="Register a new unit by serial number." />

  <Card title={<>Create a part <Method>POST</Method></>} href="/docs/api/rest-api/parts/create" description="Register a new part." />

  <Card title={<>Create a revision <Method>POST</Method></>} href="/docs/api/rest-api/parts/revisions/part_number_revisions_create" description="Add a new revision to a part." />

  <Card title={<>Create a batch <Method>POST</Method></>} href="/docs/api/rest-api/batches/create" description="Register a production cohort." />
</Cards>

## Industry standards

The TofuPilot Device Data Model is built to be compatible with established industry standards, including PLM/ERP item masters, MES batch and genealogy records, and IPC-1782 traceability.
