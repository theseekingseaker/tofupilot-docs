<!-- Source: https://www.tofupilot.com/docs/api -->
# API & SDKs
URL: /docs/api

Learn how to call TofuPilot from any language with first-party SDKs for Python, C#, Rust, C++, and MATLAB, or directly through the REST API.

***

title: API & SDKs
description: "Learn how to call TofuPilot from any language with first-party SDKs for Python, C#, Rust, C++, and MATLAB, or directly through the REST API."
-----------------------------------------------------------------------------------------------------------------------------------------------------------

import { Cards, Card } from 'fumadocs-ui/components/card';
import { IconBrandPython, IconBrandCSharp, IconBrandRust, IconBrandCpp, IconMath } from '@tabler/icons-react';
import { Code } from 'lucide-react';

Every operation you can perform on the dashboard is exposed through the TofuPilot REST API, so you can drive runs, units, and stations from any environment. You can either reach for a typed SDK in your language of choice, or hit the REST surface directly with `curl`.

Authentication runs through [API keys](/docs/access/api-keys), and every key inherits the role and access group scope of the user or station that owns it.

<Cards>
  <Card icon={<IconBrandPython />} title="Python SDK" href="/docs/sdk/python" description="First-party Python client." />

  <Card icon={<IconBrandCSharp />} title="C# SDK" href="/docs/sdk/csharp" description=".NET client for Windows and Linux benches." />

  <Card icon={<IconBrandRust />} title="Rust SDK" href="/docs/sdk/rust" description="Idiomatic Rust for embedded and CLI use." />

  <Card icon={<IconBrandCpp />} title="C++ SDK" href="/docs/sdk/cpp" description="Native client for legacy drivers and embedded code." />

  <Card icon={<IconMath />} title="MATLAB SDK" href="/docs/sdk/matlab" description="Toolbox for MATLAB test scripts." />

  <Card icon={<Code />} title="REST API" href="/docs/api/rest-api" description="Endpoints, request and response schemas, and error codes." />
</Cards>
