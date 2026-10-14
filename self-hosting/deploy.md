<!-- Source: https://www.tofupilot.com/docs/self-hosting/deploy -->
# Deploy
URL: /docs/self-hosting/deploy

Learn how to run the deploy script, create the first admin account, and start uploading runs to your self-hosted TofuPilot instance.

***

title: Deploy
description: "Learn how to run the deploy script, create the first admin account, and start uploading runs to your self-hosted TofuPilot instance."
---------------------------------------------------------------------------------------------------------------------------------------------------

import { DynamicCodeBlock } from '@/components/themed-dynamic-code-block';
import { Tabs, Tab } from 'fumadocs-ui/components/tabs';
import { Callout } from 'fumadocs-ui/components/callout';

Once `.env` is ready, re-run the deploy script. The license key is saved in `.env`, so you do not need to pass it again:

<DynamicCodeBlock lang="bash" code="curl -fsSL https://www.tofupilot.com/deploy | bash" />

The script validates your configuration, pulls the Docker images, and starts six containers.

| Container              | Role                                                           |
| ---------------------- | -------------------------------------------------------------- |
| `tofupilot-dashboard`  | TofuPilot application                                          |
| `tofupilot-pg`         | PostgreSQL                                                     |
| `tofupilot-seaweed`    | SeaweedFS file storage                                         |
| `tofupilot-proxy`      | Traefik reverse proxy with automatic HTTPS                     |
| `tofupilot-centrifugo` | Realtime WebSocket server (live status, telemetry, build logs) |
| `tofupilot-deployer`   | Build worker for the deployments pipeline (Docker-in-Docker)   |

After each deploy or update, the script reports the running version and domain to Orbit so you can monitor instances. No other data leaves your server.

<Callout type="info">
  The `tofupilot-deployer` container needs **standard (root) Docker** because it runs a privileged build sandbox. Under rootless Docker it stays down and the build pipeline is unavailable; everything else, including realtime, works normally.
</Callout>

## First login

Open your domain (e.g. `https://tofupilot.yourcompany.com`) and create your first account. That account becomes the organization admin, and each [self-hosted](/docs/self-hosting) instance supports one organization.

## Upload runs

To upload test [Runs](/docs/fundamentals/test-data-model#run), point your scripts to your instance URL. The SDK reads your [API key](/docs/api#api-key-types) from the `TOFUPILOT_API_KEY` environment variable, or you can pass it directly.

<Tabs groupId="upload-runs-v2" persist items={['OpenHTF', 'Python SDK v2']} label="">
  <Tab value="OpenHTF">
    <DynamicCodeBlock
      lang="python"
      code={`import openhtf as htf
from tofupilot.openhtf import upload

def main():
  test = htf.Test(
      procedure_id="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",  # procedure UUID from the dashboard
      part_number="PCB1",
  )
  test.add_output_callbacks(upload(
      url="https://tofupilot.yourcompany.com",
      # verify="/etc/ssl/your-ca.crt",  # if the instance uses a private CA
  ))
  test.execute(lambda: "PCB1A001")

if __name__ == '__main__':
  main()`}
    />
  </Tab>

  <Tab value="Python SDK v2">
    <DynamicCodeBlock
      lang="python"
      code={`from datetime import datetime, timezone, timedelta
from tofupilot.v2 import TofuPilot

def main():
  client = TofuPilot(
      api_key="your_api_key",
      server_url="https://tofupilot.yourcompany.com/api"
  )

  started_at = datetime.now(timezone.utc)
  ended_at = started_at + timedelta(seconds=30)

  run = client.runs.create(
      procedure_id="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",  # procedure UUID from the dashboard
      serial_number="PCB1A001",
      part_number="PCB1",
      outcome="PASS",
      started_at=started_at,
      ended_at=ended_at,
  )

  print(f"Run created: {run.id}")

if __name__ == '__main__':
  main()`}
    />
  </Tab>
</Tabs>

## Custom CA certificates

When your instance uses a self-signed or internal CA cert, tell the Python SDK to trust it.

First, export the certificate from your server:

<DynamicCodeBlock
  lang="bash"
  code={`echo | openssl s_client -connect tofupilot.yourcompany.com:443 -showcerts 2>/dev/null | \
openssl x509 -outform PEM > ca-certificate.pem`}
/>

Then pass it to the client.

<DynamicCodeBlock
  lang="python"
  code={`from tofupilot.v2 import TofuPilot
import httpx

http_client = httpx.Client(
  verify="/path/to/ca-certificate.pem"
)

client = TofuPilot(
  api_key="your_api_key",
  server_url="https://tofupilot.yourcompany.com/api",
  client=http_client
)`}
/>

### Rust SDK

The Rust SDK trusts the bundled Mozilla roots and the system certificate store, and accepts
additional root certificates directly:

<DynamicCodeBlock
  lang="rust"
  code={`use tofupilot::{ClientConfig, TofuPilot};

let config = ClientConfig::new("your_api_key")
  .base_url("https://tofupilot.yourcompany.com")
  .root_certificate_from_pem_file("/path/to/ca-certificate.pem")?;

let client = TofuPilot::with_config(config);`}
/>

`add_root_certificate` takes the PEM bytes, for a certificate that does not come from a file. It
accepts a bundle, so a root and its intermediates can be passed together:

<DynamicCodeBlock
  lang="rust"
  code={`let config = ClientConfig::new("your_api_key")
  .base_url("https://tofupilot.yourcompany.com")
  .add_root_certificate(&pem_bytes)?;`}
/>

### CLI

Pass the certificate at login. It is saved with your credentials, so every later command and the
station daemon reuse it without exporting anything into the service environment:

<DynamicCodeBlock
  lang="bash"
  code={`tofupilot login --url https://tofupilot.yourcompany.com \\
              --ca-cert /path/to/ca-certificate.pem`}
/>

`TOFUPILOT_CA_CERT=/path/to/ca-certificate.pem` overrides the saved path for a single command. The
certificate is added to the built-in roots rather than replacing them, so public hosts keep working;
there is no option to disable certificate verification.

This covers every connection the CLI makes — login, deploy, pull, run uploads, the offline queue and
the realtime link the station uses to stream live and show as online. No separate system-wide
certificate install is needed. A CA already present in the machine's trust store also works for the
realtime link, with or without this flag.

### OpenHTF output callback

<DynamicCodeBlock
  lang="python"
  code={`test.add_output_callbacks(upload(
  url="https://tofupilot.yourcompany.com",
  verify="/path/to/ca-certificate.pem",
))`}
/>

### Other clients

The C++ and MATLAB SDKs do not expose a certificate option. Point the process at your CA with the
standard OpenSSL environment variable instead:

<DynamicCodeBlock lang="bash" code={`export SSL_CERT_FILE=/path/to/ca-certificate.pem`} />
