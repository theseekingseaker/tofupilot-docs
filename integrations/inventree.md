<!-- Source: https://www.tofupilot.com/docs/integrations/inventree -->
# InvenTree
URL: /docs/integrations/inventree

Learn how to create stock items, upload test results, update stock status, transfer stock, complete build outputs, and print labels in InvenTree from TofuPilot.

***

title: InvenTree
description: "Learn how to create stock items, upload test results, update stock status, transfer stock, complete build outputs, and print labels in InvenTree from TofuPilot."
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Step, Steps } from 'fumadocs-ui/components/steps';

The InvenTree integration writes TofuPilot test data into your InvenTree instance, so build outputs, stock items, and test reports stay in sync with what happens on the factory floor.

## Connect InvenTree

You connect InvenTree once per instance using an API token that inherits the permissions of the user that generated it.

<Steps>
  <Step>
    In InvenTree, generate an API token under your user profile. The token inherits the user's permissions, so pick a user that can write to the models the workflow touches.
  </Step>

  <Step>
    In TofuPilot, open **Settings > Integrations > InvenTree** and add a connection with the InvenTree base URL and the API token.
  </Step>

  <Step>
    Click **Test connection** to confirm TofuPilot can reach the instance.
  </Step>
</Steps>

## Actions

The integration exposes six actions that cover the most common workflow patterns for production data.

| Action                    | Effect                                                                                   |
| ------------------------- | ---------------------------------------------------------------------------------------- |
| **Create stock item**     | Create a stock item for a part, or reuse the one that already carries the serial number. |
| **Upload test result**    | Record a pass/fail against a part test template on a stock item.                         |
| **Update stock status**   | Change the stock status of a stock item.                                                 |
| **Transfer stock**        | Move a stock item to another location.                                                   |
| **Complete build output** | Mark one output of a build order complete.                                               |
| **Print label**           | Print a stock item label through InvenTree.                                              |

Every picker in these actions reads live from your instance — locations, parts, build orders, build outputs, test templates, and label templates — and all six are searched against InvenTree as you type, so a large catalogue is never dumped to the browser. Any of these fields can also take a `{{variable}}` instead, in which case the value is resolved when the workflow runs.

## Identifying the stock item

**Upload test result**, **Update stock status**, **Transfer stock**, and **Print label** all act on an existing stock item, and share the same **Stock Item** field with a **Serial number** / **ID** switch next to it. New nodes start in **Serial number** mode.

| Mode              | Behavior                                                                                                                                   |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| **Serial number** | The value is looked up in InvenTree when the workflow runs. Use the trigger's serial number variable, for example `{{unit.serialNumber}}`. |
| **ID**            | The value is the numeric InvenTree primary key of the stock item.                                                                          |

In **ID** mode the value must be digits only. A value like `12-A` is refused instead of being read as `12`, so a malformed variable can never send the write to a different record. The same rule applies to every other ID field in the integration.

Serial numbers are only unique per part in InvenTree, so **Serial number** mode adds an optional **Part IPN** field. Leave it empty to search across all parts — if the serial then matches items on more than one part, the action fails and asks you to set it. Fill it, from the part picker or from a variable such as `{{part.number}}`, to scope the lookup to that part.

If a variable in the **Stock Item** or **Part IPN** field does not resolve, the action fails rather than falling back to a wider search.

## Create stock item

Pick the part by **IPN** or by numeric **ID**, using the same switch as above. In IPN mode the value is resolved against InvenTree at execution time, so you can pass the trigger's part number variable (for example `{{part.number}}`) instead of a fixed part.

| Field                        | Behavior                                                                                                                                       |
| ---------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| **Serial number** (optional) | Creates a serialized item. If a stock item with this serial already exists for the part, the action reuses it instead of creating a duplicate. |
| **Quantity**                 | Bulk stock only. Serialized items are always quantity 1. Must be a positive integer.                                                           |
| **Location** (optional)      | Where the new item is stored. Pick from the live location list or pass an ID.                                                                  |
| **Batch** (optional)         | Batch code written on the stock item.                                                                                                          |
| **Status**                   | Initial stock status, **OK** by default. See [Stock statuses](#stock-statuses).                                                                |
| **Notes** (optional)         | Free text, supports variables such as `{{run.id}}`.                                                                                            |

The find-or-create behavior on the serial number keeps re-runs idempotent, so a workflow that fires on `unit_created` can safely be retried without leaving duplicate items behind.

Reuse does not write to the item it found: **Location**, **Status**, **Batch**, and **Notes** apply only when a new item is created. Any field you configured that was not written is listed under `fieldsNotApplied` in the step result, so a re-run that quietly ignored them is visible instead of just reporting success.

The action exposes three output variables that downstream nodes can reference:

| Variable       | Type    | Value                                                                       |
| -------------- | ------- | --------------------------------------------------------------------------- |
| `stockItemId`  | number  | InvenTree primary key of the created or reused stock item.                  |
| `serialNumber` | string  | The serial number, when one was given.                                      |
| `created`      | boolean | `true` when the item was created, `false` when an existing item was reused. |

The step result also carries `partId`, the resolved part primary key, and `fieldsNotApplied` on the reuse path.

A common pattern is to chain **Create stock item** with **Upload test result** and pass `{{<nodeId>.stockItemId}}` as the stock item of the second node.

## Upload test result

InvenTree records test results against a **test template** defined on a part, not against a free-text name. Pick the template from the list — entries are labelled `test name — part (IPN)`, since the same test name usually exists on several parts. If the **Stock Item** field is in serial mode and **Part IPN** holds a fixed value, the list is narrowed to that part. The search matches the test name and its description, not the part — to narrow by part, set a fixed value in **Part IPN**.

| Field                | Behavior                                                                                                                                                                                                                      |
| -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Test Template**    | The part test template to write against. It must apply to the part of the stock item resolved at execution, directly or inherited from a parent part, otherwise the action fails and lists the templates that part does have. |
| **Result**           | **Pass** or **Fail**. On a workflow triggered by **Run created**, **From run outcome** is also offered: a `PASS` outcome is a pass, any other outcome is a fail.                                                              |
| **Value** (optional) | Measured value written alongside the result, such as a reading or a duration.                                                                                                                                                 |
| **Notes** (optional) | Free text, supports variables.                                                                                                                                                                                                |

Choosing **From run outcome** on a workflow that is not run-triggered leaves nothing to read, and the action fails with that message rather than silently recording a fail.

The action posts the stock item, the template, the result, and the optional value and notes. It does not upload attachments, and it does not send a timestamp — InvenTree stamps the result itself.

The action exposes three output variables that downstream nodes can reference:

| Variable       | Type   | Value                                                              |
| -------------- | ------ | ------------------------------------------------------------------ |
| `stockItemId`  | number | InvenTree primary key of the stock item the result was written to. |
| `testName`     | string | Test name read back from the template, not from your input.        |
| `testResultId` | number | Primary key of the test result record InvenTree created.           |

## Update stock status

Identify the stock item, then pick the new status. The action writes the status straight onto the stock item record.

The action exposes two output variables that downstream nodes can reference:

| Variable      | Type   | Value                                                                               |
| ------------- | ------ | ----------------------------------------------------------------------------------- |
| `stockItemId` | number | InvenTree primary key of the stock item that was updated.                           |
| `status`      | number | Status code read back from the updated item. See [Stock statuses](#stock-statuses). |

### Stock statuses

The status dropdown offers InvenTree's built-in stock status codes:

| Code | Status           |
| ---- | ---------------- |
| `10` | OK               |
| `50` | Attention needed |
| `55` | Damaged          |
| `60` | Destroyed        |
| `65` | Rejected         |
| `70` | Lost             |
| `75` | Quarantined      |
| `85` | Returned         |

Instances that define custom status codes are not listed here.

## Transfer stock

Identify the stock item, then set the destination.

| Field                | Behavior                                                                    |
| -------------------- | --------------------------------------------------------------------------- |
| **Location**         | Destination stock location. Pick from the live location list or pass an ID. |
| **Notes** (optional) | Free text written on the stock movement, supports variables.                |

The transfer moves the item's whole quantity, which the action reads from InvenTree rather than asking you for it.

The action exposes two output variables that downstream nodes can reference:

| Variable      | Type   | Value                                                   |
| ------------- | ------ | ------------------------------------------------------- |
| `stockItemId` | number | InvenTree primary key of the stock item that was moved. |
| `locationId`  | number | Destination location the item was moved to.             |

## Complete build output

This action completes one specific output of one build order, so it needs both — there is no inference from the build alone.

| Field                   | Behavior                                                                                                                                                                                                                |
| ----------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Build Order**         | Outstanding InvenTree build orders.                                                                                                                                                                                     |
| **Output**              | In-progress outputs of the selected build, labelled by serial, or by batch and quantity. The list only loads once **Build Order** holds a concrete ID; if the build is bound to a variable, bind the output to one too. |
| **Location** (optional) | Where the completed output is stored.                                                                                                                                                                                   |
| **Notes** (optional)    | Free text, supports variables.                                                                                                                                                                                          |

The action exposes three output variables that downstream nodes can reference:

| Variable     | Type   | Value                                                 |
| ------------ | ------ | ----------------------------------------------------- |
| `buildId`    | number | Build order the output was completed against.         |
| `outputId`   | number | Build output that was completed.                      |
| `locationId` | number | Where the output was stored, when a location was set. |

## Print label

| Field                 | Behavior                                                                                                                 |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| **Label Template**    | Stock item label templates read from your instance.                                                                      |
| **Stock Item**        | The item to print for. Identified by serial number or ID, see [Identifying the stock item](#identifying-the-stock-item). |
| **Plugin** (optional) | Printing plugin key. Defaults to the server's built-in printer.                                                          |

The action exposes three output variables that downstream nodes can reference:

| Variable      | Type   | Value                                                                  |
| ------------- | ------ | ---------------------------------------------------------------------- |
| `stockItemId` | number | InvenTree primary key of the stock item the label was printed for.     |
| `templateId`  | number | Label template used for the print job.                                 |
| `file`        | string | URL of the generated label file, when the printing plugin returns one. |

## Limits

The InvenTree action inherits TofuPilot's HTTP caps and the per-token throttles your InvenTree instance applies.

* InvenTree's own per-token rate limits apply.
* Picker lists are capped, so a large catalogue is never fetched whole. Narrow with the search box, or pass the ID or IPN directly if what you want is not offered.
* Action runtime and retries match the [HTTP action](/docs/integrations/http).
