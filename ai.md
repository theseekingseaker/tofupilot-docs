<!-- Source: https://www.tofupilot.com/docs/ai -->
# TofuPilot AI
URL: /docs/ai

Ask questions about your test data, run autonomous root cause analysis, and get notified the moment yield, volume, or sample behavior drifts.

***

title: TofuPilot AI
description: "Ask questions about your test data, run autonomous root cause analysis, and get notified the moment yield, volume, or sample behavior drifts."
preview: true
-------------

import { Cards, Card } from 'fumadocs-ui/components/card';
import { MessageSquare, FlaskConical } from 'lucide-react';

TofuPilot AI turns your test data into answers and actions. Chat queries every run, unit, and measurement in plain English; root cause analysis investigates an incident autonomously; alerts watch the production line and page the right people when something breaks.

<Cards>
  <Card icon={<MessageSquare />} title="Chat" href="/docs/ai/chat" description="Ask questions across runs, units, parts, and measurements." />

  <Card icon={<FlaskConical />} title="Root Cause Analysis" href="/docs/ai/root-cause-analysis" description="Run an autonomous root cause analysis on failing units." />
</Cards>

## AI provider

TofuPilot AI runs on open-source large language models hosted by our partner [Infomaniak](https://www.infomaniak.com/) in Switzerland. Every prompt, tool call, and response stays inside the European Union and the Swiss legal framework. Your data is not shared with OpenAI, Anthropic, Google, or any third-party model provider, and it is never used to train a foundation model.

| Property        | Value                                                     |
| --------------- | --------------------------------------------------------- |
| Model provider  | Infomaniak AI Tools                                       |
| Hosting region  | Switzerland                                               |
| Data residency  | EU + Swiss legal framework                                |
| Training opt-in | No. Your prompts and data are never used to train models. |
| Sub-processors  | Infomaniak only. No US-based AI provider in the chain.    |

If your compliance team requires a different setup, the [self-hosted deployment](/docs/self-hosting) lets you point TofuPilot AI at any OpenAI-compatible endpoint you operate yourself.
