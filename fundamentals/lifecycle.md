<!-- Source: https://www.tofupilot.com/docs/fundamentals/lifecycle -->
# Test Lifecycle
URL: /docs/fundamentals/lifecycle

Follow a hardware test through all four stages on TofuPilot: author the procedure, deploy it to a station, run it on the line, and analyze the results.

***

title: Test Lifecycle
description: "Follow a hardware test through all four stages on TofuPilot: author the procedure, deploy it to a station, run it on the line, and analyze the results."
----------------------------------------------------------------------------------------------------------------------------------------------------------------------

import { WorkflowDiagramLazy } from '@/components/workflow-diagram-lazy';

On TofuPilot, hardware tests follow a continuous cycle of four stages: **author, deploy, run, analyze**.

<WorkflowDiagramLazy />

## Author

A procedure declares the test logic TofuPilot executes. Three formats are supported:

* [Supported framework](/docs/frameworks): Run OpenHTF, Pytest, or Robot suites natively.
* [TofuPilot Framework](/docs/frameworks/tofupilot): YAML + Python with phases, plugs, operator UI, and parallel execution built in.
* [Custom test code](/docs/sdk): Drive your own test loop and upload runs through a typed SDK (Python, C#, Rust, C++, MATLAB) or the REST API.

Procedures are edited in your code editor, and supported frameworks can be run in the [CLI](/docs/cli) to test before deploying.

## Deploy

Two deployment paths are supported:

* **Git-backed** for the TofuPilot Framework and supported frameworks: A push to a connected Git repo creates a [Deployment](/docs/deployments). When auto-push is on, a push to the production branch rolls out to every [Station](/docs/station) linked to the procedure. Supported providers:
  * [GitHub](/docs/deployments/git)
  * [GitLab](/docs/deployments/git) (Cloud or self-hosted)
  * [Bitbucket](/docs/deployments/git) (Data Center / Server)
* **[Custom test code](/docs/sdk)**: Bring your existing deployment pipeline. Authenticate the bench with a [station API key](/docs/access/api-keys) so runs land under the right station.

Stations reconcile their checkout between runs, so operators never see a half-applied update.

## Run

### Execution

Every run walks the same sequence:

1. **Identify the unit**: Resolve the serial against the [unit configuration](/docs/fundamentals/device-data-model#unit).
2. **Initialize plugs**: Each [Plug](/docs/frameworks/tofupilot) starts in its own subprocess. A failure aborts the run.
3. **Setup phases**: Calibration and warm-up. A failure aborts the run.
4. **Main phases**: Parallel where independent, serialized by `depends_on`, bounded by per-phase timeouts.
5. **Teardown phases**: Always run. Power down, release fixtures, dispose resources.

Supported framework connectors cover a subset of this sequence. See the [framework feature matrix](/docs/frameworks#frameworks-features-support-matrix) for which framework supports what.

### Operator UI

A phase can pause and prompt an operator for input (visual inspection, manual action) or stream live values back to the dashboard. Inputs block until submitted, displays update in real time, and the same component tree renders in the [Terminal UI](/docs/station/terminal-ui), [Remote UI](/docs/station/remote-ui), or [Kiosk UI](/docs/station/kiosk-ui) depending on the station setup.

### Outcome

* **Resolve outcome**: Worst phase wins: `ERROR` → `ABORTED` → `TIMEOUT` → `FAIL` → `PASS`.
* **Upload**: Send the payload (procedure, unit, phases, measurements, logs, attachments) through the REST API. Stations queue offline and drain on reconnect with the original timestamp.

## Analyze

Every uploaded run feeds the dashboard. Use these views to diagnose production issues:

| View                                                           | Use for                                                                           |
| -------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| [Yield](/docs/run-analytics/run-analytics)                     | Spot FPY, SPY, TPY, LPY trends across procedures, stations, batches, revisions.   |
| [Phase pareto](/docs/run-analytics/phase-pareto)               | Rank phases by failure count and duration to find weak steps.                     |
| [Measurement control](/docs/run-analytics/measurement-control) | Watch Cpk and control charts on every measurement to catch drift.                 |
| [Run explorer](/docs/run-analytics/run-explorer)               | Open the full report of any run with phases, measurements, logs, and attachments. |
| [Unit explorer](/docs/run-analytics/unit-explorer)             | Trace any unit by serial number across its lifetime.                              |

Use the insights to improve test coverage, stability, or yield in the next [Author](#author) cycle.
