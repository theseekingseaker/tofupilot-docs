<!-- Source: https://www.tofupilot.com/docs/sdk/cpp -->
# Using the TofuPilot C++ SDK
URL: /docs/sdk/cpp

Learn how to call the TofuPilot REST API from C++17 with fluent builders, typed exceptions, and a CMake FetchContent install.

***

title: Using the TofuPilot C++ SDK
description: "Learn how to call the TofuPilot REST API from C++17 with fluent builders, typed exceptions, and a CMake FetchContent install."
--------------------------------------------------------------------------------------------------------------------------------------------

The `tofupilot` C++ SDK is a header-and-source library that wraps the TofuPilot REST API. Targets C++17, depends on `nlohmann/json`, `cpp-httplib`, and OpenSSL. Source on GitHub at [tofupilot/cpp](https://github.com/tofupilot/cpp), MIT licensed.

## Installation

Pin a tag with `FetchContent` and link the `tofupilot` target.

```cmake title="CMakeLists.txt"
include(FetchContent)
FetchContent_Declare(
    tofupilot
    GIT_REPOSITORY https://github.com/tofupilot/cpp.git
    GIT_TAG v2.5.0
    GIT_SHALLOW TRUE
)
FetchContent_MakeAvailable(tofupilot)
target_link_libraries(your_target PRIVATE tofupilot)
```

## Authentication

Pass your API key to the constructor:

```cpp title="quickstart.cpp"
#include <tofupilot/tofupilot.hpp>

auto client = tofupilot::TofuPilot("<YOUR_API_KEY_HERE>");
```

Or read the env var:

```bash title="terminal"
export TOFUPILOT_API_KEY="your-api-key"
```

```cpp title="quickstart.cpp"
#include <tofupilot/tofupilot.hpp>
#include <iostream>

int main() {
    auto client = tofupilot::TofuPilot(std::getenv("TOFUPILOT_API_KEY"));

    auto run = client.runs().create()
        .procedure_id("your-procedure-id")
        .serial_number("SN001")
        .part_number("PN001")
        .outcome(tofupilot::Outcome::Pass)
        .send();

    std::cout << "Run created: " << run.id << std::endl;
}
```

## Examples

Each method maps to a REST endpoint. The [REST API reference](/docs/api/rest-api) sidebar lists all endpoints with C++ snippets. Common workflows below.

### Create a run with measurements

Build a phase struct, add measurements to it, and pass the phase to the run builder.

```cpp title="create_run.cpp"
tofupilot::RunCreateMeasurements m;
m.name = "Output Voltage";
m.outcome = tofupilot::ValidatorsOutcome::Pass;
m.measured_value = nlohmann::json(3.3);

tofupilot::RunCreatePhases phase;
phase.name = "Voltage Test";
phase.outcome = tofupilot::PhasesOutcome::Pass;
phase.started_at = "2026-01-01T00:00:00Z";
phase.ended_at = "2026-01-01T00:05:00Z";
phase.measurements = {m};

auto run = client.runs().create()
    .procedure_id("your-procedure-id")
    .serial_number("SN-001")
    .part_number("PCB-V1")
    .outcome(tofupilot::Outcome::Pass)
    .started_at("2026-01-01T00:00:00Z")
    .ended_at("2026-01-01T00:05:00Z")
    .phases({phase})
    .send();
```

### List and filter runs

Chain filters on the list builder. Pagination is cursor-based: `result.meta.has_more` and `result.meta.next_cursor`.

```cpp title="list_runs.cpp"
std::optional<int64_t> cursor;
while (true) {
    auto builder = client.runs().list()
        .part_numbers({"PCB-V1"})
        .outcomes({tofupilot::Outcome::Pass})
        .limit(50);
    if (cursor) builder.cursor(*cursor);

    auto result = builder.send();
    for (const auto& run : result.data) {
        std::cout << run.id << " - " << run.unit.serial_number << std::endl;
    }

    if (!result.meta.has_more) break;
    cursor = result.meta.next_cursor;
}
```

### Upload and download attachments

Sub-resource helpers wrap the three-step REST flow (initialize, PUT to pre-signed URL, finalize) into one call.

**Run attachments**, on `client.runs().attachments()`:

| Method     | Signature                                                         | Returns                       |
| ---------- | ----------------------------------------------------------------- | ----------------------------- |
| `upload`   | `upload(const std::string& run_id, const std::string& path)`      | `std::string` (attachment ID) |
| `download` | `download(const std::string& url, const std::string& local_path)` | `void`                        |

**Unit attachments**, on `client.units().attachments()`:

| Method     | Signature                                                                        | Returns                        |
| ---------- | -------------------------------------------------------------------------------- | ------------------------------ |
| `upload`   | `upload(const std::string& serial_number, const std::string& path)`              | `std::string` (attachment ID)  |
| `download` | `download(const std::string& url, const std::string& local_path)`                | `void`                         |
| `delete_`  | `delete_(const std::string& serial_number, const std::vector<std::string>& ids)` | `UnitDeleteAttachmentResponse` |

```cpp title="attachments.cpp"
auto attachment_id = client.runs().attachments().upload(run.id, "report.pdf");

auto fetched = client.runs().get().id(run.id).send();
client.runs().attachments().download(fetched.attachments[0].download_url, "report-copy.pdf");

auto unit_attachment_id = client.units().attachments().upload("SN-0001", "calibration.pdf");
client.units().attachments().delete_("SN-0001", {unit_attachment_id});
```

`delete_` carries a trailing underscore because `delete` is a reserved word in C++.

## Error handling

The SDK throws typed exceptions for each HTTP status. Every exception carries the parsed error body.

```cpp title="errors.cpp"
try {
    auto run = client.runs().get().id("nonexistent").send();
} catch (const tofupilot::NotFoundError& e) {
    std::cerr << "Not found: " << e.error().message << std::endl;
} catch (const tofupilot::BadRequestError& e) {
    for (const auto& issue : e.error().issues)
        std::cerr << "  - " << issue.message << std::endl;
} catch (const tofupilot::ApiException& e) {
    std::cerr << "API error " << e.status_code() << ": " << e.what() << std::endl;
}
```

Each exception type maps to a specific HTTP status code.

| Exception                   | Status |
| --------------------------- | ------ |
| `BadRequestError`           | 400    |
| `UnauthorizedError`         | 401    |
| `ForbiddenError`            | 403    |
| `NotFoundError`             | 404    |
| `ConflictError`             | 409    |
| `UnprocessableContentError` | 422    |
| `RateLimitedError`          | 429    |
| `InternalServerError`       | 500    |
| `BadGatewayError`           | 502    |

## Thread safety

The `TofuPilot` client is not thread-safe. Use one client per thread or synchronize access externally.

## Retries

The client retries `429`, `502`, `503`, and `504` with exponential backoff (3 retries by default). Configure via `ClientConfig::set_max_retries`.

```cpp title="retries.cpp"
auto client = tofupilot::TofuPilot(
    tofupilot::ClientConfig::with_api_key("your-api-key")
        .set_max_retries(5)
);
```

## User agent

Override the user agent string via `ClientConfig::set_user_agent` to attribute requests to your integration.

```cpp title="user_agent.cpp"
auto client = tofupilot::TofuPilot(
    tofupilot::ClientConfig::with_api_key("your-api-key")
        .set_user_agent("my-test-bench/1.2.3")
);
```

## Per-call overrides

Pass a `RequestConfig` to `.send(...)` to override the server URL or timeout for a single call without rebuilding the client.

```cpp title="overrides.cpp"
tofupilot::RequestConfig opts;
opts.server_url = "https://staging.tofupilot.app/api";
opts.timeout = std::chrono::seconds(120);

auto result = client.runs().list().send(opts);
```

## Self-hosted

To target a [self-hosted](/docs/self-hosting) instance, pass the instance origin plus `/api` via `ClientConfig::set_base_url` (the SDK appends `/v2/...` per call).

```cpp title="self_hosted.cpp"
auto client = tofupilot::TofuPilot(
    tofupilot::ClientConfig::with_api_key("your-api-key")
        .set_base_url("https://your-instance.example.com/api")
);
```
