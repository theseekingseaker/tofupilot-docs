<!-- Source: https://www.tofupilot.com/docs/ai/root-cause-analysis -->
# Root Cause Analysis
URL: /docs/ai/root-cause-analysis

Learn how TofuPilot finds the cause behind a yield drop or measurement drift: an automatic analysis that tests hypotheses against your runs and returns a verdict.

***

title: Root Cause Analysis
description: "Learn how TofuPilot finds the cause behind a yield drop or measurement drift: an automatic analysis that tests hypotheses against your runs and returns a verdict."
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Callout } from 'fumadocs-ui/components/callout';

An alert tells you a line degraded. A **root cause analysis** tells you why. Launched in one click from the alert, it combs through the runs behind the incident, tests candidate causes against your data, and returns a verdict backed by evidence, with concrete next steps. It follows the 6M method — man, machine, material, method, measurement, environment — testing each probable cause statistically against a baseline, and digging deeper into the branches the evidence supports.

## Supported alerts

An analysis starts from an alert — one of the two trend types:

| Alert type            | The analysis asks                                               |
| --------------------- | --------------------------------------------------------------- |
| **Yield drop**        | Which units failed, and what do the failures have in common?    |
| **Measurement drift** | Which factor does the shift in the drifting measurement follow? |

Other alert types can't be analyzed yet.

## Analyzed runs

The analysis looks at the runs behind the alert: the degraded stretch, plus the healthy runs before it as a baseline to compare against. On a drift alert, it focuses on the measurement that moved. If your own rule fired the alert, your threshold is what the conclusion answers to.

## The verdict

Every finished analysis lands on one of seven verdicts.

| Verdict                    | Meaning                                                         |
| -------------------------- | --------------------------------------------------------------- |
| **Single probable cause**  | One cause explains the incident.                                |
| **Independent causes**     | Several causes, each acting on its own.                         |
| **Interaction of factors** | Causes that only bite in combination.                           |
| **Confounded causes**      | Probable causes that cannot be separated with this data.        |
| **Partial explanation**    | A finding that does not cover the whole incident.               |
| **Inconclusive**           | No probable cause could be isolated.                            |
| **Not enough data**        | Not enough data and/or metadata to explore the possible causes. |

## The hypothesis tree

Results are organized like a fishbone diagram from a quality review: possible causes branch out from the incident, and each branch is tested against your data. Each cause carries a status:

| Status                  | Meaning                                                                                     |
| ----------------------- | ------------------------------------------------------------------------------------------- |
| **Probable**            | The evidence supports it. A probable cause with no probable child is a **candidate cause**. |
| **Unlikely / Rejected** | The evidence points away from it, or rules it out.                                          |
| **Not enough data**     | Untestable with today's runs.                                                               |

Click any cause to see the evidence — the checks and charts behind it.

## Next steps

A finished analysis ends with two lists:

* **To solve the issue** — what to check or fix for the causes found.
* **To improve the analysis** — what to add in TofuPilot so the next analysis can test more.

## Visibility

An analysis belongs to the alert, not to the person who ran it: anyone who opens the alert sees the latest result, signed with the name of whoever launched it. A teammate's re-run replaces yours, and vice versa.

<Callout type="info">
  **Roles and permissions.** An analysis runs with the access of the person who launched it: it reads only the data their [role](/docs/access/access-roles) and access group allow. Two teammates with different scopes can therefore reach different results on the same alert.
</Callout>
