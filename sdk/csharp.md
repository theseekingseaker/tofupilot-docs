<!-- Source: https://www.tofupilot.com/docs/sdk/csharp -->
# Using the TofuPilot C# SDK
URL: /docs/sdk/csharp

Learn how to call the TofuPilot REST API from .NET 8 with async/await, CancellationToken support, and a custom HTTP handler for client certificates.

***

title: Using the TofuPilot C# SDK
description: "Learn how to call the TofuPilot REST API from .NET 8 with async/await, CancellationToken support, and a custom HTTP handler for client certificates."
-------------------------------------------------------------------------------------------------------------------------------------------------------------------

The `TofuPilot` NuGet package is a type-safe .NET 8 SDK that wraps the TofuPilot REST API. Source on GitHub at [tofupilot/csharp](https://github.com/tofupilot/csharp), MIT licensed.

## Installation

Install from NuGet:

```bash title="terminal"
dotnet add package TofuPilot
```

## Authentication

Pass your API key to the constructor:

```csharp title="Run.cs"
using TofuPilot;

var client = new TofuPilot(apiKey: "<YOUR_API_KEY_HERE>");
```

Or set the env var, which you read on construction:

```bash title="terminal"
export TOFUPILOT_API_KEY="your-api-key"
```

```csharp title="Quickstart.cs"
using TofuPilot;
using TofuPilot.Models.Requests;

var client = new TofuPilot(
    apiKey: Environment.GetEnvironmentVariable("TOFUPILOT_API_KEY")!
);

var run = await client.Runs.CreateAsync(new RunCreateRequest
{
    ProcedureId = "your-procedure-id",
    SerialNumber = "SN001",
    PartNumber = "PN001",
    Outcome = RunCreateOutcome.Pass,
    StartedAt = DateTime.UtcNow.AddMinutes(-5),
    EndedAt = DateTime.UtcNow,
});

Console.WriteLine($"Run created: {run.Id}");
```

### Dynamic API keys

Pass `apiKeySource: Func<string>` instead of `apiKey:` to evaluate the key on every request. Useful for short-lived credentials.

```csharp title="DynamicKey.cs"
var client = new TofuPilot(
    apiKeySource: () => ReadKeyFromVault()
);
```

## Examples

Each method maps to a REST endpoint. The [REST API reference](/docs/api/rest-api) sidebar lists all endpoints with C# snippets. Common workflows below.

### Create a run with measurements

Nest a phase with measurements and validators under `Phases`.

```csharp title="CreateRun.cs"
var run = await client.Runs.CreateAsync(new RunCreateRequest
{
    ProcedureId = procedureId,
    SerialNumber = "SN-001",
    PartNumber = "PCB-V1",
    Outcome = RunCreateOutcome.Pass,
    StartedAt = DateTime.UtcNow.AddMinutes(-5),
    EndedAt = DateTime.UtcNow,
    Phases = new List<RunCreatePhases>
    {
        new()
        {
            Name = "Voltage Test",
            Outcome = RunCreatePhasesOutcome.Pass,
            StartedAt = DateTime.UtcNow.AddMinutes(-5),
            EndedAt = DateTime.UtcNow,
            Measurements = new List<RunCreateMeasurements>
            {
                new()
                {
                    Name = "Output Voltage",
                    Outcome = RunCreateMeasurementsOutcome.Pass,
                    MeasuredValue = 3.3,
                    Units = RunCreateUnits.CreateStr("V"),
                    Validators = new List<RunCreateMeasurementsValidators>
                    {
                        new() { Operator = ">=", ExpectedValue = RunCreateMeasurementsExpectedValue.CreateNumber(3.0) },
                        new() { Operator = "<=", ExpectedValue = RunCreateMeasurementsExpectedValue.CreateNumber(3.6) },
                    },
                },
            },
        },
    },
});
```

### List and filter runs

Filter `ListAsync` by any supported field. Pagination is cursor-based: `result.Meta.HasMore` and `result.Meta.NextCursor`.

```csharp title="ListRuns.cs"
long? cursor = null;
while (true)
{
    var result = await client.Runs.ListAsync(
        partNumbers: new List<string> { "PCB-V1" },
        outcomes: new List<RunListQueryParamOutcome> { RunListQueryParamOutcome.Pass },
        limit: 50,
        cursor: cursor
    );

    foreach (var run in result.Data)
        Console.WriteLine($"{run.Id} - {run.Unit.SerialNumber}");

    if (!result.Meta.HasMore) break;
    cursor = result.Meta.NextCursor;
}
```

### Upload and download attachments

Extension methods on `IRuns` and `IUnits` wrap the three-step REST flow (initialize, PUT to pre-signed URL, finalize) into one call. Access them via `Runs.Attachments()` and `Units.Attachments()`.

**Run attachments**, on `client.Runs.Attachments()`:

| Method          | Signature                                                                                   | Returns                        |
| --------------- | ------------------------------------------------------------------------------------------- | ------------------------------ |
| `UploadAsync`   | `UploadAsync(string runId, string filePath, CancellationToken ct = default)`                | `Task<string>` (attachment ID) |
| `DownloadAsync` | `DownloadAsync(string downloadUrl, string destinationPath, CancellationToken ct = default)` | `Task<string>` (path)          |

**Unit attachments**, on `client.Units.Attachments()`:

| Method          | Signature                                                                                   | Returns                              |
| --------------- | ------------------------------------------------------------------------------------------- | ------------------------------------ |
| `UploadAsync`   | `UploadAsync(string serialNumber, string filePath, CancellationToken ct = default)`         | `Task<string>` (attachment ID)       |
| `DownloadAsync` | `DownloadAsync(string downloadUrl, string destinationPath, CancellationToken ct = default)` | `Task<string>` (path)                |
| `DeleteAsync`   | `DeleteAsync(string serialNumber, List<string> ids, CancellationToken ct = default)`        | `Task<UnitDeleteAttachmentResponse>` |

```csharp title="Attachments.cs"
var attachmentId = await client.Runs.Attachments().UploadAsync(runId, "report.pdf");

var run = await client.Runs.GetAsync(runId);
await client.Runs.Attachments().DownloadAsync(run.Attachments[0].DownloadUrl, "report-copy.pdf");

var unitAttachmentId = await client.Units.Attachments().UploadAsync("SN-0001", "calibration.pdf");
await client.Units.Attachments().DeleteAsync("SN-0001", new List<string> { unitAttachmentId });
```

Helper behavior:

* `UploadAsync` throws `FileNotFoundException` if the path does not exist, `InvalidOperationException` if the pre-signed PUT fails.
* `DownloadAsync` throws `ArgumentException` if `downloadUrl` is null or empty, `InvalidOperationException` if the GET fails.

## Error handling

Errors raise typed exceptions in `TofuPilot.Models.Errors`. The base `ApiException` carries the status code and raw response body for fallback handling.

```csharp title="Errors.cs"
using TofuPilot.Models.Errors;

try
{
    await client.Runs.GetAsync("nonexistent-id");
}
catch (NotFoundException ex)
{
    Console.WriteLine($"Not found: {ex.Message}");
}
catch (BadRequestException ex)
{
    Console.WriteLine($"Bad request: {ex.Message}");
}
catch (ApiException ex)
{
    Console.WriteLine($"API error {ex.StatusCode}: {ex.Body}");
}
```

Each exception type maps to a specific HTTP status code.

| Exception                       | Status    |
| ------------------------------- | --------- |
| `BadRequestException`           | 400       |
| `UnauthorizedException`         | 401       |
| `ForbiddenException`            | 403       |
| `NotFoundException`             | 404       |
| `ConflictException`             | 409       |
| `UnprocessableContentException` | 422       |
| `InternalServerErrorException`  | 500       |
| `ApiException`                  | Any other |

## Cancellation

Every async method takes an optional `CancellationToken` as the final argument.

```csharp title="Cancellation.cs"
using var cts = new CancellationTokenSource(TimeSpan.FromSeconds(10));
var run = await client.Runs.CreateAsync(request, cts.Token);
```

## Custom certificates

For mTLS, inject a `TofuPilotHttpClient` backed by a configured `HttpClientHandler` that loads a client certificate.

```csharp title="Mtls.cs"
using System.Security.Cryptography.X509Certificates;
using TofuPilot.Utils;

var handler = new HttpClientHandler();
handler.ClientCertificates.Add(new X509Certificate2("client.pfx", "password"));

var client = new TofuPilot(
    apiKey: "your-api-key",
    client: new TofuPilotHttpClient(handler)
);
```

## Hooks

Register hooks to inspect or modify requests, successful responses, and errors. Implement `IBeforeRequestHook`, `IAfterSuccessHook`, or `IAfterErrorHook` and register them on the SDK configuration.

```csharp title="Hooks.cs"
using TofuPilot.Hooks;

class LoggingHook : IBeforeRequestHook
{
    public Task<HttpRequestMessage> BeforeRequestAsync(BeforeRequestContext ctx, HttpRequestMessage request)
    {
        Console.WriteLine($"[{ctx.OperationID}] {request.Method} {request.RequestUri}");
        return Task.FromResult(request);
    }
}

var client = new TofuPilot(apiKey: "your-api-key");
client.SDKConfiguration.Hooks.RegisterBeforeRequestHook(new LoggingHook());
```

`RegisterAfterSuccessHook` and `RegisterAfterErrorHook` follow the same pattern.

## Self-hosted

To target a [self-hosted](/docs/self-hosting) instance, pass `serverUrl` as the instance origin plus `/api` (the SDK appends `/v2/...` per call).

```csharp title="SelfHosted.cs"
var client = new TofuPilot(
    apiKey: "your-api-key",
    serverUrl: "https://your-instance.example.com/api"
);
```
