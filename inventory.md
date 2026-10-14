<!-- Source: https://www.tofupilot.com/docs/inventory -->
# Inventory
URL: /docs/inventory

Learn how TofuPilot Inventory anchors every run to a physical thing on the factory floor with parts, revisions, units, and batches.

***

title: Inventory
description: "Learn how TofuPilot Inventory anchors every run to a physical thing on the factory floor with parts, revisions, units, and batches."
--------------------------------------------------------------------------------------------------------------------------------------------------

import { Cards, Card } from 'fumadocs-ui/components/card';
import { ScanQrCode, Component, Package, Hash } from 'lucide-react';

TofuPilot Inventory is how the platform tracks the physical world. A **Part** is a product family, a **Revision** is a design version, a **Unit** is one physical instance, and a **Batch** is a production cohort. Every run links to one unit, and every unit links to one part revision.

<Cards>
  <Card icon={<ScanQrCode />} title="Units" href="/docs/inventory/units" description="Devices under test, identified by serial number. Carry typed metadata, attachments, sub-unit hierarchies, and a sample flag." />

  <Card icon={<Component />} title="Parts and revisions" href="/docs/inventory/parts" description="Product families and their design Revisions. Use Revisions to compare yield across hardware generations." />

  <Card icon={<Package />} title="Batches" href="/docs/inventory/batches" description="Production cohorts. Group units made together to slice yield by manufacturing lot." />

  <Card icon={<Hash />} title="Serial numbers" href="/docs/inventory/serial-numbers" description="Auto-extract a part number from every unit's serial number with an organization-wide regex." />
</Cards>

## Identity graph

The four inventory records form a small graph that links every run back to a physical object.

```
Part  ──▶  Revision  ──▶  Unit  ──▶  Run
                          │
                  optional│
                          ▼
                        Batch
```

A unit always has a part and a revision, and it may have a batch and a parent unit when it belongs to an assembly.

## Auto-creation

You rarely need to create inventory by hand. The first run against a new serial creates the unit, part, revision, and batch (when `batch_number` is sent) in one transaction, and names default to placeholders you can edit later.

You can also create records up front from the [API](/docs/api/rest-api) or the dashboard, which is useful for legacy imports, reference samples staged before production, or pre-allocated serial ranges.

## Constraints

A handful of constraints apply to every inventory record.

* **Identifier format**: `^[a-zA-Z0-9_.:+-]+$`, 1-60 characters. The same rule applies to serials, part numbers, revisions, and batches.
* **Uniqueness**: per-organization and case-insensitive.
* **Metadata**: up to 50 typed keys (string, number, boolean) per unit, part, and revision, with 40-character keys.
* **Cascades**: deleting a part deletes its revisions, units, and runs. Deleting a batch unsets `batch_number` on its units but keeps the units themselves.
