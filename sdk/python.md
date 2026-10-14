<!-- Source: https://www.tofupilot.com/docs/sdk/python -->
# Using the TofuPilot Python SDK
URL: /docs/sdk/python

Learn how to call the TofuPilot REST API from Python with a typed client that covers runs, units, parts, procedures, batches, stations, and attachments.

***

title: Using the TofuPilot Python SDK
description: "Learn how to call the TofuPilot REST API from Python with a typed client that covers runs, units, parts, procedures, batches, stations, and attachments."
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

The `tofupilot` package is a type-safe Python SDK that wraps the TofuPilot REST API. Source on GitHub at [tofupilot/python](https://github.com/tofupilot/python), MIT licensed.

## Installation

Install from PyPI:

```bash title="terminal"
pip install tofupilot
```

## Authentication

Pass your API key to the constructor:

```python title="run.py"
from tofupilot.v2 import TofuPilot

client = TofuPilot(api_key="<YOUR_API_KEY_HERE>")
```

Or set the env var, which the client reads on construction:

```bash title="terminal"
export TOFUPILOT_API_KEY="your-api-key"
```

```python title="quickstart.py"
import os
from tofupilot.v2 import TofuPilot

with TofuPilot(api_key=os.getenv("TOFUPILOT_API_KEY")) as client:
    run = client.runs.create(
        procedure_id="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        serial_number="SN001",
        part_number="PN001",
        outcome="PASS",
    )
    print(f"Run created: {run.id}")
```

Exiting the `with` block closes the HTTP session.

### Dynamic API keys

`api_key` accepts a callable, called on every request. Useful for short-lived credentials.

```python title="dynamic_key.py"
from tofupilot.v2 import TofuPilot

def load_key() -> str:
    return read_key_from_vault()

client = TofuPilot(api_key=load_key)
```

## Examples

Each method maps to a REST endpoint. The [REST API reference](/docs/api/rest-api) sidebar lists all endpoints with Python snippets. Common workflows below.

### Create a run with measurements

Nest a phase with measurements and validators under `phases`.

```python title="create_run.py"
from datetime import datetime, timedelta, timezone

run = client.runs.create(
    procedure_id=procedure_id,
    serial_number="SN-001",
    part_number="PCB-V1",
    outcome="PASS",
    started_at=datetime.now(timezone.utc) - timedelta(minutes=5),
    ended_at=datetime.now(timezone.utc),
    phases=[{
        "name": "Voltage Test",
        "outcome": "PASS",
        "started_at": datetime.now(timezone.utc) - timedelta(minutes=5),
        "ended_at": datetime.now(timezone.utc),
        "measurements": [{
            "name": "Output Voltage",
            "outcome": "PASS",
            "measured_value": 3.3,
            "units": "V",
            "validators": [
                {"operator": ">=", "expected_value": 3.0},
                {"operator": "<=", "expected_value": 3.6},
            ],
        }],
    }],
)
```

### List and filter runs

Filter `list` by any supported field. Pagination is cursor-based: `result.meta.has_more` and `result.meta.next_cursor`.

```python title="list_runs.py"
cursor = None
while True:
    result = client.runs.list(
        part_numbers=["PCB-V1"],
        outcomes=["PASS"],
        limit=50,
        cursor=cursor,
    )
    for run in result.data:
        print(f"{run.id} - {run.unit.serial_number}")
    if not result.meta.has_more:
        break
    cursor = result.meta.next_cursor
```

### Upload and download attachments

Sub-resource helpers wrap the three-step REST flow (initialize, PUT to pre-signed URL, finalize) into one call.

**Run attachments**, on `client.runs.attachments`:

| Method     | Signature                                                | Returns               |
| ---------- | -------------------------------------------------------- | --------------------- |
| `upload`   | `upload(id: str, file: str \| Path)`                     | `str` (attachment ID) |
| `download` | `download(attachment, dest: str \| Path \| None = None)` | `Path`                |

**Unit attachments**, on `client.units.attachments`:

| Method     | Signature                                                | Returns               |
| ---------- | -------------------------------------------------------- | --------------------- |
| `upload`   | `upload(serial_number: str, file: str \| Path)`          | `str` (attachment ID) |
| `download` | `download(attachment, dest: str \| Path \| None = None)` | `Path`                |
| `delete`   | `delete(serial_number: str, ids: list[str])`             | response object       |

```python title="attachments.py"
attachment_id = client.runs.attachments.upload(id=run_id, file="report.pdf")

run = client.runs.get(id=run_id)
local_path = client.runs.attachments.download(run.attachments[0], dest="report-copy.pdf")

unit_attachment_id = client.units.attachments.upload(serial_number="SN-0001", file="calibration.pdf")
client.units.attachments.delete(serial_number="SN-0001", ids=[unit_attachment_id])
```

Helper behavior:

* `upload` raises `FileNotFoundError` if the path does not exist, `RuntimeError` if the pre-signed PUT fails.
* `download` raises `ValueError` if the attachment has no `download_url`.
* When `dest` is omitted, `download` writes to the attachment's `name` in the current directory.

## Error handling

Errors map to typed exceptions you import from `tofupilot.v2.errors`.

```python title="errors.py"
from tofupilot.v2.errors import ErrorNOTFOUND, ErrorBADREQUEST

try:
    client.runs.get(id="nonexistent-id")
except ErrorNOTFOUND as e:
    print(f"Not found: {e.message}")
except ErrorBADREQUEST as e:
    print(f"Bad request: {e.message}")
```

Each exception type maps to a specific HTTP status code.

| Exception                   | Status |
| --------------------------- | ------ |
| `ErrorBADREQUEST`           | 400    |
| `ErrorUNAUTHORIZED`         | 401    |
| `ErrorFORBIDDEN`            | 403    |
| `ErrorNOTFOUND`             | 404    |
| `ErrorCONFLICT`             | 409    |
| `ErrorUNPROCESSABLECONTENT` | 422    |
| `ErrorINTERNALSERVERERROR`  | 500    |

## Async methods

Every method has an `_async` counterpart. The client also supports `async with`, which closes the underlying `httpx.AsyncClient` on exit.

```python title="async.py"
import asyncio
from tofupilot.v2 import TofuPilot

async def main():
    async with TofuPilot(api_key="your-api-key") as client:
        run = await client.runs.create_async(
            procedure_id="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            serial_number="SN001",
            part_number="PN001",
            outcome="PASS",
        )
        print(run.id)

asyncio.run(main())
```

## Retries and timeouts

Set globally on the client or per call. Import `RetryConfig` and `BackoffStrategy` from `tofupilot.v2.utils`.

```python title="retries.py"
from tofupilot.v2 import TofuPilot
from tofupilot.v2.utils import BackoffStrategy, RetryConfig

client = TofuPilot(
    api_key="your-api-key",
    timeout_ms=30_000,
    retry_config=RetryConfig(
        strategy="backoff",
        backoff=BackoffStrategy(
            initial_interval=500,
            max_interval=10_000,
            exponent=1.5,
            max_elapsed_time=60_000,
        ),
        retry_connection_errors=True,
    ),
)
```

The SDK retries on `429`, `500`, `502`, `503`, `504` by default.

## Per-call overrides

Override per call without touching the client. Useful for long uploads, custom headers, or a different host.

```python title="overrides.py"
from tofupilot.v2.utils import BackoffStrategy, RetryConfig

run = client.runs.get(
    id=run_id,
    timeout_ms=60_000,
    http_headers={"X-Request-Source": "nightly-job"},
    server_url="https://staging.example.com/api",
    retries=RetryConfig(
        strategy="backoff",
        backoff=BackoffStrategy(500, 5_000, 1.5, 20_000),
        retry_connection_errors=True,
    ),
)
```

## Hooks

Register hooks to inspect or modify requests, successful responses, and errors. Implement the `BeforeRequestHook`, `AfterSuccessHook`, or `AfterErrorHook` protocols and register them on the SDK configuration.

```python title="hooks.py"
import httpx
from tofupilot.v2 import TofuPilot
from tofupilot.v2._hooks.types import BeforeRequestHook, BeforeRequestContext

class LoggingHook(BeforeRequestHook):
    def before_request(self, ctx: BeforeRequestContext, request: httpx.Request) -> httpx.Request:
        print(f"[{ctx.operation_id}] {request.method} {request.url}")
        return request

client = TofuPilot(api_key="your-api-key")
client.sdk_configuration._hooks.register_before_request_hook(LoggingHook())
```

`register_after_success_hook` and `register_after_error_hook` follow the same pattern.

## Custom HTTP client

Inject a custom `httpx.Client` to configure proxies, mTLS, connection limits, or retries beyond the SDK defaults. Pass `client=` (sync) or `async_client=` (async).

```python title="custom_http.py"
import httpx
from tofupilot.v2 import TofuPilot

http = httpx.Client(
    proxy="http://proxy.example.com:8080",
    verify="/path/to/ca-bundle.pem",
    timeout=60,
)

client = TofuPilot(api_key="your-api-key", client=http)
```

## Debug logging

Pass a `debug_logger` to surface request and response details for troubleshooting.

```python title="debug.py"
import logging
from tofupilot.v2 import TofuPilot
from tofupilot.v2.utils import get_default_logger

logging.basicConfig(level=logging.DEBUG)
client = TofuPilot(api_key="your-api-key", debug_logger=get_default_logger())
```

## Self-hosted

To target a [self-hosted](/docs/self-hosting) instance, pass `server_url` as the instance origin plus `/api` (the SDK appends `/v2/...` per call).

```python title="self_hosted.py"
client = TofuPilot(
    api_key="your-api-key",
    server_url="https://your-instance.example.com/api",
)
```
