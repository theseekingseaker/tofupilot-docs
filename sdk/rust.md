<!-- Source: https://www.tofupilot.com/docs/sdk/rust -->
# Using the TofuPilot Rust SDK
URL: /docs/sdk/rust

Learn how to call the TofuPilot REST API from async Rust with a builder pattern, automatic retries on 429/5xx, and configurable lifecycle hooks.

***

title: Using the TofuPilot Rust SDK
description: "Learn how to call the TofuPilot REST API from async Rust with a builder pattern, automatic retries on 429/5xx, and configurable lifecycle hooks."
---------------------------------------------------------------------------------------------------------------------------------------------------------------

The `tofupilot` crate is a type-safe async Rust SDK that wraps the TofuPilot REST API. Source on GitHub at [tofupilot/rust](https://github.com/tofupilot/rust), MIT licensed.

## Installation

Add the crate to your `Cargo.toml` along with `tokio` for the async runtime. Requires Rust 1.75+.

```toml title="Cargo.toml"
[dependencies]
tofupilot = "2.10"
tokio = { version = "1", features = ["full"] }
```

## Authentication

Pass your API key to `TofuPilot::new`:

```rust title="src/main.rs"
use tofupilot::TofuPilot;

let client = TofuPilot::new("<YOUR_API_KEY_HERE>");
```

Or read the env var:

```bash title="terminal"
export TOFUPILOT_API_KEY="your-api-key"
```

```rust title="quickstart.rs"
use tofupilot::TofuPilot;
use tofupilot::types::*;

#[tokio::main]
async fn main() -> tofupilot::Result<()> {
    let key = std::env::var("TOFUPILOT_API_KEY").unwrap();
    let client = TofuPilot::new(&key);

    let run = client.runs().create()
        .procedure_id("your-procedure-id")
        .serial_number("SN001")
        .part_number("PN001")
        .outcome(Outcome::Pass)
        .send()
        .await?;

    println!("Run created: {}", run.id);
    Ok(())
}
```

## Examples

Each method maps to a REST endpoint. The [REST API reference](/docs/api/rest-api) sidebar lists all endpoints with Rust snippets. Common workflows below.

### Create a run with measurements

Nest a phase with measurements and validators under `phases`. Required fields are checked at `.send()` time.

```rust title="create_run.rs"
use tofupilot::types::*;

let now = chrono::Utc::now();

let run = client.runs().create()
    .procedure_id(proc_id)
    .serial_number("SN-001")
    .part_number("PCB-V1")
    .outcome(Outcome::Pass)
    .started_at(now - chrono::TimeDelta::minutes(5))
    .ended_at(now)
    .phases(vec![RunCreatePhases::builder()
        .name("voltage_check")
        .outcome(PhasesOutcome::Pass)
        .started_at(now - chrono::TimeDelta::minutes(5))
        .ended_at(now)
        .measurements(vec![RunCreateMeasurements::builder()
            .name("output_voltage")
            .outcome(ValidatorsOutcome::Pass)
            .measured_value(3.3)
            .units("V")
            .build()
            .unwrap()])
        .build()
        .unwrap()])
    .send()
    .await?;
```

### List and filter runs

Filter `list` by any supported field. Pagination is cursor-based: `runs.meta.has_more` and `runs.meta.next_cursor`.

```rust title="list_runs.rs"
let mut cursor: Option<i64> = None;
loop {
    let mut req = client.runs().list()
        .part_numbers(vec!["PCB-V1".into()])
        .outcomes(vec![Outcome::Pass])
        .limit(50);
    if let Some(c) = cursor {
        req = req.cursor(c);
    }
    let runs = req.send().await?;

    for run in &runs.data {
        println!("{} - {}", run.id, run.unit.serial_number);
    }

    if !runs.meta.has_more { break; }
    cursor = runs.meta.next_cursor;
}
```

### Upload and download attachments

Sub-resource helpers wrap the three-step REST flow (initialize, PUT to pre-signed URL, finalize) into one call. Content-Type is auto-detected from the file extension.

**Run attachments**, on `client.runs().attachments()`:

| Method     | Signature                                      | Returns                          |
| ---------- | ---------------------------------------------- | -------------------------------- |
| `upload`   | `upload(run_id: &str, path: impl AsRef<Path>)` | `Result<String>` (attachment ID) |
| `download` | `download(url: &str, dest: impl AsRef<Path>)`  | `Result<()>`                     |

**Unit attachments**, on `client.units().attachments()`:

| Method     | Signature                                             | Returns                                |
| ---------- | ----------------------------------------------------- | -------------------------------------- |
| `upload`   | `upload(serial_number: &str, path: impl AsRef<Path>)` | `Result<String>` (attachment ID)       |
| `download` | `download(url: &str, dest: impl AsRef<Path>)`         | `Result<()>`                           |
| `delete`   | `delete(serial_number: &str, ids: Vec<String>)`       | `Result<UnitDeleteAttachmentResponse>` |

```rust title="attachments.rs"
let attachment_id = client.runs().attachments().upload(&run.id, "report.pdf").await?;

let run = client.runs().get().id(&run_id).send().await?;
client.runs().attachments().download(&run.attachments[0].download_url, "report-copy.pdf").await?;

let unit_attachment_id = client.units().attachments().upload("SN-0001", "calibration.pdf").await?;
client.units().attachments().delete("SN-0001", vec![unit_attachment_id]).await?;
```

## Error handling

Match on the typed `Error` enum.

```rust title="errors.rs"
use tofupilot::Error;

match client.runs().get().id("nonexistent").send().await {
    Ok(run) => println!("Found: {}", run.id),
    Err(Error::NotFound(e)) => println!("Not found: {}", e.message),
    Err(Error::Unauthorized(e)) => println!("Bad API key: {}", e.message),
    Err(Error::BadRequest(e)) => {
        println!("Validation error: {}", e.message);
        for issue in &e.issues {
            println!("  - {}", issue.message);
        }
    }
    Err(e) => println!("Other error: {e}"),
}
```

Each variant maps to a specific HTTP status code.

| Variant                | Status |
| ---------------------- | ------ |
| `BadRequest`           | 400    |
| `Unauthorized`         | 401    |
| `Forbidden`            | 403    |
| `NotFound`             | 404    |
| `Conflict`             | 409    |
| `UnprocessableContent` | 422    |
| `RateLimited`          | 429    |
| `InternalServerError`  | 500    |
| `BadGateway`           | 502    |

Transport-level variants `Http`, `Json`, `Io`, `Validation`, `UnexpectedStatus { status, body }` cover the remaining cases.

## Retries and timeouts

The client retries `429`, 5xx, and connection errors with exponential backoff (3 retries by default). Override via `ClientConfig`.

```rust title="retries.rs"
use tofupilot::TofuPilot;
use tofupilot::config::ClientConfig;
use std::time::Duration;

let client = TofuPilot::with_config(
    ClientConfig::new("your-api-key")
        .timeout(Duration::from_secs(60))
        .max_retries(5),
);
```

## Hooks

Inspect or modify requests, successful responses, and errors with lifecycle hooks.

```rust title="hooks.rs"
use tofupilot::{Hooks, TofuPilot};
use tofupilot::config::ClientConfig;

let hooks = Hooks::new()
    .on_before_request(|ctx, req| async move {
        println!("[{}] {} {}", ctx.operation_id, req.method(), req.url());
        req
    })
    .on_after_success(|ctx, response| async move {
        println!("[{}] OK", ctx.operation_id);
    })
    .on_after_error(|ctx, err| async move {
        eprintln!("[{}] Error: {err}", ctx.operation_id);
    });

let client = TofuPilot::with_config(
    ClientConfig::new("your-api-key").hooks(hooks),
);
```

## Per-call overrides

Every request builder accepts `.server_url(...)` and `.timeout(...)` to override the client defaults for a single call.

```rust title="overrides.rs"
let result = client.runs().list()
    .server_url("https://staging.tofupilot.app/api")
    .timeout(std::time::Duration::from_secs(120))
    .send()
    .await?;
```

## Nullable fields

Some fields distinguish "not sent" from "explicitly null". The typed builder methods cover both cases, so you never construct `NullableField` by hand.

```rust title="nullable.rs"
client.runs().create()
    .procedure_version("1.2.3")    // sets Value("1.2.3")
    .procedure_version_null()       // sets Null
    // omitted fields default to Absent
```

## Self-hosted

To target a [self-hosted](/docs/self-hosting) instance, pass `base_url` on `ClientConfig` as the instance origin plus `/api` (the SDK appends `/v2/...` per call).

```rust title="self_hosted.rs"
use tofupilot::TofuPilot;
use tofupilot::config::ClientConfig;

let client = TofuPilot::with_config(
    ClientConfig::new("your-api-key")
        .base_url("https://your-instance.example.com/api"),
);
```
