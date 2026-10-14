<!-- Source: https://www.tofupilot.com/docs/fundamentals/test-data-model -->
# Test Data Model
URL: /docs/fundamentals/test-data-model

The data model behind every run: phases, typed measurements, aggregations, validators, logs, and attachments.

***

title: Test Data Model
description: "The data model behind every run: phases, typed measurements, aggregations, validators, logs, and attachments."
----------------------------------------------------------------------------------------------------------------------------

import { DataModelDiagramLazy } from '@/components/data-model-diagram-lazy';
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

The TofuPilot Test Data Model is designed to scale with your test scenarios: from a simple run with a pass/fail outcome to complex tests with multiple phases, typed measurements, aggregations, validators, logs, attachments, and more.

<DataModelDiagramLazy />

<div className="sr-only" aria-hidden="false">
  ```
  Procedure (e.g. FVT-PCB)
  └── Deployment / Version (e.g. v1.2)
      └── Run → outcome: PASS | FAIL | ERROR | TIMEOUT | ABORTED
          └── Phase (e.g. Measure Voltage)
              ├── Scalar Measurement (typed value, e.g. 5.03 V)
              │   └── Validator (operator + expected, e.g. ≤ 5.2 V)
              ├── Multi-dimensional Measurement (X axis + Y axes, e.g. waveform)
              │   └── Aggregation (mean, min, max, std, custom)
              │       └── Validator (operator + expected, e.g. mean ≤ 5.0 V)
              ├── Log (DEBUG | INFO | WARNING | ERROR | CRITICAL)
              └── Attachment (binary file, e.g. qc-inspection.jpg)
  ```
</div>

## Procedure

A procedure is the test definition. It declares which [phases](#phase) run and which [measurements](#measurement) get collected, and produces one [run](#run) per execution against a [unit](/docs/fundamentals/device-data-model#unit).

### Deployment

A [deployment](/docs/deployments) is a build of a procedure tied to one git commit. Every push produces a new deployment automatically, and each [run](#run) records the deployment it executed against.

### Version

A version is a user-settable label on a procedure, typically `x.y.z`. You bump the version by hand when the test definition meaningfully changes.

## Run

A run is one execution of a procedure on one [unit](/docs/fundamentals/device-data-model#unit). At minimum it records an outcome; it can also carry [phases](#phase), [measurements](#measurement), [logs](#log), and [attachments](#attachment).

## Phase

A phase is one step inside a run, and can contain [measurements](#measurement), [logs](#log), and [attachments](#attachment). Each phase produces one outcome, and the run outcome is the worst phase outcome.

## Measurement

A measurement is a typed value collected by a phase. Each measurement is scored by one or more [validators](#validator).

### Scalar

A scalar measurement holds a single value: a number, a string, a boolean, or an object.

### Multi-dimensional

A multi-dimensional measurement holds one X axis and one or more Y axes, used for waveforms, sweeps, and time-series.

## Validator

A validator scores a measurement or an [aggregation](#aggregation) against an expected value. Any failure fails the measurement, the phase, and the run.

## Aggregation

An aggregation reduces a multi-dimensional measurement to a scalar such as a mean or a max. Aggregations can carry validators.

## Log

A log is a structured message recorded during a phase. Logs are visible on the run page and searchable across runs.

## Attachment

An attachment is a binary file persisted alongside a run: a photo, a scope capture, a diagnostic dump, or a PDF report. Each attachment belongs to one run, and can also be linked to the specific phase that produced it.

## Schema reference

The full schema and endpoints for every entity are documented in the [REST API reference](/docs/api/rest-api).

<Cards>
  <Card title={<>Create a run <Method>POST</Method></>} href="/docs/api/rest-api/runs/create" description="Stream a finished test run from a station or script." />

  <Card title={<>Create an attachment <Method>POST</Method></>} href="/docs/api/rest-api/runs/attach_create" description="Upload reports, logs, or images to a run." />

  <Card title={<>Create a procedure <Method>POST</Method></>} href="/docs/api/rest-api/procedures/create" description="Register a new test definition." />

  <Card title={<>Create a procedure version <Method>POST</Method></>} href="/docs/api/rest-api/procedures/versions/create" description="Publish a new version of a procedure." />
</Cards>

## Industry standards

The TofuPilot Test Data Model is built to be compatible with established industry standards, including NI TestStand sequences, STDF v4 records, and ATML (IEEE 1671) reports.
