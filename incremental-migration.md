<!-- Source: https://www.tofupilot.com/docs/incremental-migration -->
# Incremental Migration
URL: /docs/incremental-migration

Learn how to migrate an existing test suite to TofuPilot one procedure, one station, or one product line at a time, with zero downtime.

***

title: Incremental Migration
description: "Learn how to migrate an existing test suite to TofuPilot one procedure, one station, or one product line at a time, with zero downtime."
------------------------------------------------------------------------------------------------------------------------------------------------------

import { MigrationDiagramLazy } from '@/components/migration-diagram-lazy';
import { StrategyDiagramLazy } from '@/components/strategy-diagram-lazy';

When migrating to TofuPilot, use an incremental migration strategy. Your legacy system and TofuPilot run simultaneously, and you move parts of your test stack over at a pace that suits your factory.

<MigrationDiagramLazy />

<div className="sr-only" aria-hidden="false">
  ```
  Today          [============== Legacy system ==============>]
  Increment 1    [=========== Legacy system =========>]
                 [===== New ====>]
  Increment 2    [====== Legacy =====>]
                 [====== New ======>]
  Increment 3    [== Legacy ==>]
                 [=========== New ============>]
  Migration done [============== New system =================>]
  ```
</div>

## Why opt for incremental migration?

Incremental migrations offer several advantages:

* Reduced risk due to smaller migration steps
* A smoother rollback path in case of unexpected issues
* Earlier technical implementation and production value validation
* Downtime-free migration without maintenance windows

### Disadvantages of one-time migrations

One-time migration involves developing the new test stack separately before cutting every station over. This approach has certain drawbacks:

* Late discovery of expensive procedure or station issues
* Difficulty in assessing migration success upfront
* Potential for reaching a point of no-return, even with major problem detection
* Possible production loss due to legacy system downtime during migration

## Incremental migration strategies

With incremental migration, your legacy system and TofuPilot operate simultaneously. Depending on your strategy, you select an aspect of your test stack, like an integration layer or a single procedure, to migrate incrementally.

<StrategyDiagramLazy />

<div className="sr-only" aria-hidden="false">
  ```
  Vertical: fill column by column (layer by layer across all procedures)
  Horizontal: fill row by row (one procedure end-to-end)

                    File import   SDK upload   Framework   Auto-deploy
    Procedure A        [X]           [X]          [X]          [X]
    Procedure B        [X]           [X]          [X]          [X]
    Procedure C        [X]           [X]          [X]          [X]
  ```
</div>

### Vertical migration

Migrate one integration layer at a time, across all procedures:

1. Start with file imports for legacy runs.
2. Move to direct SDK uploads from the legacy runner.
3. Migrate the procedure onto a supported framework.
4. Connect Git and enable auto-deploy on every push.

Each procedure climbs the ladder at its own pace until every one reaches auto-deploy.

### Horizontal migration

Migrate one whole procedure end-to-end before starting the next:

1. List every legacy procedure you still run.
2. Pick one and migrate it end-to-end.
3. Repeat with the next procedure.

A subset of procedures runs fully on TofuPilot while the rest stay on the legacy stack until their turn.

### Hybrid migration

A blend of both. For each procedure subset, run it through every integration layer before moving to the next subset.
