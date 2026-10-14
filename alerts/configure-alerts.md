<!-- Source: https://www.tofupilot.com/docs/alerts/configure-alerts -->
# Configure Alerts
URL: /docs/alerts/configure-alerts

Configure alert rules and email notifications for TofuPilot alerts. Set custom thresholds and scope rules to procedures, stations, or parts.

***

title: Configure Alerts
description: "Configure alert rules and email notifications for TofuPilot alerts. Set custom thresholds and scope rules to procedures, stations, or parts."
-----------------------------------------------------------------------------------------------------------------------------------------------------------

import { Step, Steps } from 'fumadocs-ui/components/steps';

Use alert rules to notify your team when TofuPilot detects yield drops, measurement drift, reference-sample failures, or retest storms.

## The default alert rule

Every organization has a rule named **Default Alert Rule**. It monitors every standard alert type with automatic detection across all procedures, stations, and parts, and covers new alert types as they ship. Email is off until you enable it.

You can change its severity filter, scope, and email level. Its name and criteria are fixed, and it cannot be deleted. Disabling it turns off automatic monitoring for the organization.

## Configure alert rules

TofuPilot-defined minimum activity thresholds reduce low-volume noise and are not configured in the alert rule form. For details, see [How alerts trigger](/docs/alerts#how-alerts-trigger).

<Steps>
  <Step>
    Open **Alerts** in the sidebar and click **Configure Alerts**, or go to `/{org}/~/alerts/configure`.
  </Step>

  <Step>
    Click **New Rule**.
  </Step>

  <Step>
    Enter a rule name. This name appears in the rules list and in email notifications.
  </Step>

  <Step>
    Select the alert type to configure. A rule watches exactly one type.
  </Step>

  <Step>
    Under trigger criteria, keep **Automatic** or select **Custom** and set a threshold for each severity line. An unchecked severity line never fires. Thresholds must be ordered by severity: critical trips at or above warning, warning at or above info. Cpk floor inverts, since a lower Cpk is worse.
  </Step>

  <Step>
    Configure the filters. Set the minimum severity the rule routes, and optionally scope the rule to specific procedures, stations, or parts. An empty filter applies to everything.
  </Step>

  <Step>
    Under notifications, choose the email level: **Off**, **Critical only**, **Warning and above**, or **All severities**.
  </Step>

  <Step>
    Save the rule.
  </Step>
</Steps>

For yield drop and measurement drift, scoping a rule to a station or part does more than filter: the rule grades that slice of the data against the slice's own baseline, so a problem confined to one fixture fires even when pooled numbers look fine.

### Configure run failed alerts

Create a separate alert rule when you want to monitor consecutive failed runs. This keeps automatic detection in place on your default rule while tuning a separate rule for consecutive failures; run failed has no automatic mode.

After you start creating an alert rule, select **Run failed** as the alert type and set the consecutive-failure threshold for each severity line. Scope the rule narrowly: on a line with normal failures it will flood your alerts. For quality regressions on ordinary lines, use yield drop instead.

## Configure email notifications

Alert email goes to the organization owners unless individual recipients are configured on the rule. Members who leave the organization stop receiving alert email immediately.

The Alerts page always shows every severity a rule fires. Email is a separate severity floor per rule:

| Email level           | Sends                                           |
| --------------------- | ----------------------------------------------- |
| **Off**               | Nothing. Alerts appear on the Alerts page only. |
| **Critical only**     | Critical.                                       |
| **Warning and above** | Warning and critical.                           |
| **All severities**    | Every alert, including info.                    |

Each episode sends at most one email per severity level. An escalation sends once and threads under the original message. The alert's activity feed records each delivery as pending, sent, or failed.

## Edit, delete, or restore rules

| Action              | Effect on open alerts                                                                                                  |
| ------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| **Criteria edited** | Nothing resolves. Trend alerts re-grade within a minute under the new criteria. Event alerts re-grade on the next run. |
| **Rule disabled**   | Alerts no other rule fires are resolved. Re-enable the rule and they re-fire within a minute if still breaching.       |
| **Rule deleted**    | Same as disabling.                                                                                                     |

Deleted rules move to **Recently deleted** at the bottom of the rules list for 30 days and can be restored with their criteria intact. After 30 days they are permanently deleted.
