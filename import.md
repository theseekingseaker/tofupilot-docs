<!-- Source: https://www.tofupilot.com/docs/import -->
# Import
URL: /docs/import

Learn how to import existing test results into TofuPilot from OpenHTF, WATS, STDF, ATML, CSV, and Excel files without changing your test code.

***

title: Import
type: conceptual
description: "Learn how to import existing test results into TofuPilot from OpenHTF, WATS, STDF, ATML, CSV, and Excel files without changing your test code."
related:

* /docs/fundamentals/test-data-model
* /docs/inventory
* /docs/api/rest-api
* /docs/frameworks/openhtf

***

import { Cards, Card } from 'fumadocs-ui/components/card';
import { OpenHtfIcon, WatsIcon, ExcelIcon, CsvIcon, TeststandIcon, IeeeIcon, SemiIcon } from '@repo/ui/components/provider-icons';

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

Import converts test files from your old platform into TofuPilot [runs](/docs/fundamentals/test-data-model#run), so you can migrate historical data without rewriting your test code.

Upload a supported format from the dashboard or the [API](/docs/api/rest-api) in one step. For arbitrary layouts like CSV and Excel, build a reusable field mapping once, then apply it to every later file from the dashboard or the API.

## Supported formats

TofuPilot reads two kinds of file. **Tabular** formats use arbitrary column names, so you map your columns to TofuPilot fields once and save that mapping as a reusable preset. **Structured** formats carry a fixed, known schema, so TofuPilot detects the format and maps every field automatically.

### Tabular

<Cards>
  <Card icon={<CsvIcon />} title="CSV" href="/docs/import/csv" description="Comma-separated values, mapped to fields." />

  <Card icon={<ExcelIcon />} title="Excel" href="/docs/import/excel" description="Workbooks, through the same mapping as CSV." />
</Cards>

### Structured

<Cards>
  <Card icon={<SemiIcon />} title="ATDF" href="/docs/import/atdf" description="The ASCII text form of STDF datalogs." />

  <Card icon={<IeeeIcon />} title="ATML" href="/docs/import/atml" description="IEEE ATML XML (versions 2.02, 5.00, 6.01)." />

  <Card icon={<TeststandIcon />} title="NI TestStand" href="/docs/import/teststand" description="NI TestStand native XML." />

  <Card icon={<OpenHtfIcon />} title="OpenHTF" href="/docs/import/openhtf" description="OpenHTF JSON logs." />

  <Card icon={<SemiIcon />} title="STDF" href="/docs/import/stdf" description="STDF semiconductor ATE datalogs." />

  <Card icon={<WatsIcon />} title="WATS WSXF" href="/docs/import/wsxf" description="Virinco WATS reports in XML." />

  <Card icon={<WatsIcon />} title="WSJF" href="/docs/import/wsjf" description="Virinco WATS reports in JSON." />
</Cards>

## Import methods

You can import files from the dashboard or through the API. Both attach the imported runs to a [procedure](/docs/fundamentals/test-data-model#procedure) you choose.

### Dashboard

The import page lives under a procedure. Open a procedure, go to its import view, then:

1. **Add files.** Drag and drop, or click to browse. The dropzone accepts structured and tabular files in one batch.
2. **Review the detected format.** Each row shows its detected format. Structured files are marked **Ready**; tabular files are marked **Needs mapping**.
3. **Map tabular files.** Click a tabular row to open the mapping sidebar, then pick a saved preset or build a new mapping (see [CSV](/docs/import/csv)).
4. **Import.** Every **Ready** and **Mapped** file imports in one action.

### API

Upload each file to get an `upload_id`, then call the endpoint for its kind. Authenticate with an [API key](/docs/access/api-keys).

<Cards>
  <Card title={<>Import structured files <Method>POST</Method></>} href="/docs/api/rest-api/imports/create_structured" description="Import 1 to 100 structured files in one call. Each file is parsed independently and returns its own result." />

  <Card title={<>Import a tabular file <Method>POST</Method></>} href="/docs/api/rest-api/imports/create_tabular" description="Import one CSV or Excel file with an inline mapping or a saved template." />
</Cards>

Each format page lists its own API call. See the [REST API reference](/docs/api/rest-api) for full request and response schemas.

## When your format isn't supported

If your equipment writes a format TofuPilot doesn't read, convert it to a run yourself and post it to the [create run](/docs/api/rest-api/runs/create) endpoint. The run schema is the same one every importer targets, so a converter only has to map your file's fields onto it.

1. **Write a converter.** Read your file, then build a run payload: a serial number, an outcome, timing, and the phases and measurements you want to keep.
2. **Post the run.** Call `POST /v2/runs` directly, or use a typed [SDK](/docs/sdk) such as Python so the run schema is checked at compile time and you get autocomplete for every field.
3. **Repeat in a loop.** Point the converter at a directory to backfill historical files in bulk.

The fastest way to write a converter is to hand the job to a coding agent. Give it a sample of your file, a link to the [run schema](/docs/api/rest-api/runs/create), and the [TofuPilot LLM context](/llms.txt), and ask it to generate an upload script. The agent maps your fields to the schema and writes the loop for you.

## How import works

TofuPilot parses each file into one or more runs through the same path a live test uses, so an imported run is indistinguishable from one a station produced. The first time it sees a new serial number, it [auto-creates](/docs/inventory#auto-creation) the unit, part, revision, and batch.

A single file can produce many runs. Wafer and lot datalogs (STDF, ATDF) and multi-report XML (WATS WSXF, NI TestStand) yield one run per part or report, so a full lot lands as a batch of runs in one import.

Each file is parsed independently. A malformed file is rejected on its own without failing the rest of the batch, and within a multi-run file, the runs that parse cleanly are still imported even if others fail.
