<!-- Source: https://www.tofupilot.com/docs/sdk/matlab -->
# Using the TofuPilot MATLAB SDK
URL: /docs/sdk/matlab

Learn how to call the TofuPilot REST API from MATLAB R2019b+ with struct-based requests, automatic retries on 429/5xx, and lifecycle hooks.

***

title: Using the TofuPilot MATLAB SDK
description: "Learn how to call the TofuPilot REST API from MATLAB R2019b+ with struct-based requests, automatic retries on 429/5xx, and lifecycle hooks."
----------------------------------------------------------------------------------------------------------------------------------------------------------

The TofuPilot MATLAB SDK is a type-safe toolbox that wraps the TofuPilot REST API. Requests are plain MATLAB structs and responses are decoded structs. Source on GitHub at [tofupilot/matlab](https://github.com/tofupilot/matlab), MIT licensed.

## Installation

Clone the repo and add it to your MATLAB path. Requires MATLAB R2019b or later, no extra toolboxes.

```bash title="terminal"
git clone https://github.com/tofupilot/matlab.git
```

```matlab title="setup.m"
addpath('/path/to/matlab');
savepath;
```

## Authentication

Pass your API key as the first argument to the constructor:

```matlab title="run.m"
sdk = tofupilot.TofuPilot('<YOUR_API_KEY_HERE>');
```

Or read the env var:

```bash title="terminal"
export TOFUPILOT_API_KEY="your-api-key"
```

```matlab title="quickstart.m"
sdk = tofupilot.TofuPilot(getenv('TOFUPILOT_API_KEY'));

req.procedure_id = 'your-procedure-id';
req.serial_number = 'SN001';
req.part_number = 'PN001';
req.outcome = 'PASS';

run = sdk.Runs.create(req);
fprintf('Run created: %s\n', run.id);
```

## Examples

Each method maps to a REST endpoint. The [REST API reference](/docs/api/rest-api) sidebar lists all endpoints with MATLAB snippets. Common workflows below.

### Create a run with measurements

Nest a phase with measurements under `phases`. Use cell-of-struct (`{{...}}`) wrapping so MATLAB encodes the JSON array correctly.

```matlab title="create_run.m"
now = char(datetime('now', 'TimeZone', 'UTC', 'Format', 'yyyy-MM-dd''T''HH:mm:ss''Z'''));

measurement = struct( ...
    'name', 'output_voltage', ...
    'outcome', 'PASS', ...
    'measured_value', 3.3, ...
    'unit', 'V');

phase = struct( ...
    'name', 'voltage_check', ...
    'outcome', 'PASS', ...
    'started_at', now, ...
    'ended_at', now, ...
    'measurements', {{measurement}});

req.procedure_id = 'your-procedure-id';
req.serial_number = 'SN-001';
req.part_number = 'PCB-V1';
req.outcome = 'PASS';
req.started_at = now;
req.ended_at = now;
req.phases = {{phase}};

run = sdk.Runs.create(req);
```

### List and filter runs

Pass filters as name-value pairs to `list`. Pagination is cursor-based: `result.meta.has_more` and `result.meta.next_cursor`.

```matlab title="list_runs.m"
cursor = [];
while true
    if isempty(cursor)
        result = sdk.Runs.list('partNumbers', {'PCB-V1'}, 'outcomes', {'PASS'}, 'limit', 50);
    else
        result = sdk.Runs.list('partNumbers', {'PCB-V1'}, 'outcomes', {'PASS'}, 'limit', 50, 'cursor', cursor);
    end

    for i = 1:numel(result.data)
        run = result.data(i);
        fprintf('%s - %s\n', run.id, run.unit.serial_number);
    end

    if ~result.meta.has_more
        break;
    end
    cursor = result.meta.next_cursor;
end
```

### Upload and download attachments

Static helpers on `tofupilot.AttachmentHelpers` wrap the three-step REST flow (initialize, PUT to pre-signed URL, finalize) into one call. Pass the root `sdk` object: the finalize step records the file's size and content type and needs `sdk.Attachments`. The pre-2.9 signature taking `sdk.Runs` / `sdk.Units` still uploads but skips finalize, so those attachments show size 0.

| Method         | Signature                                   | Returns                |
| -------------- | ------------------------------------------- | ---------------------- |
| `attachToRun`  | `attachToRun(sdk, runId, filePath)`         | `char` (attachment ID) |
| `attachToUnit` | `attachToUnit(sdk, serialNumber, filePath)` | `char` (attachment ID) |
| `download`     | `download(downloadUrl, destinationPath)`    | `char` (path)          |

```matlab title="attachments.m"
attachmentId = tofupilot.AttachmentHelpers.attachToRun(sdk, run.id, 'report.pdf');

run = sdk.Runs.get(run.id);
tofupilot.AttachmentHelpers.download(run.attachments(1).download_url, 'report-copy.pdf');

unitAttachmentId = tofupilot.AttachmentHelpers.attachToUnit(sdk, 'SN-0001', 'calibration.pdf');
sdk.Units.deleteAttachment('SN-0001', 'ids', {unitAttachmentId});
```

Helpers throw `MException` with identifiers `tofupilot:FileNotFound`, `tofupilot:InvalidUrl`, or `tofupilot:UploadFailed` on failure.

## Error handling

HTTP failures rethrow the underlying `MException`. Match on `identifier` or status from the response body.

```matlab title="errors.m"
try
    sdk.Runs.get('nonexistent-id');
catch err
    fprintf('Error: %s\n', err.message);
    if contains(err.identifier, 'NotFound')
        fprintf('Run does not exist.\n');
    end
end
```

## Retries

The client retries `429` and 5xx responses with exponential backoff (3 retries at 1s, 2s, 4s by default). Override via `MaxRetries`.

```matlab title="retries.m"
sdk = tofupilot.TofuPilot('your-api-key', 'MaxRetries', 5);

% Disable retries
sdk = tofupilot.TofuPilot('your-api-key', 'MaxRetries', 0);
```

## Hooks

Attach lifecycle hooks to log or instrument requests, successful responses, and errors.

```matlab title="hooks.m"
sdk = tofupilot.TofuPilot('your-api-key', ...
    'BeforeRequest', {@(ctx) fprintf('-> %s %s\n', ctx.method, ctx.path)}, ...
    'AfterSuccess', {@(ctx, resp) fprintf('OK\n')}, ...
    'AfterError', {@(ctx, err) fprintf('Error: %s\n', err.message)});
```

## Configuration

All configuration is passed as name-value pairs on the constructor; per-call overrides are not supported.

```matlab title="config.m"
sdk = tofupilot.TofuPilot('your-api-key', ...
    'BaseUrl', 'https://your-server.com/api', ...
    'Timeout', 60, ...
    'MaxRetries', 5);
```

## Self-hosted

To target a [self-hosted](/docs/self-hosting) instance, pass `BaseUrl` as the instance origin plus `/api` (the SDK appends `/v2/...` per call).

```matlab title="self_hosted.m"
sdk = tofupilot.TofuPilot('your-api-key', ...
    'BaseUrl', 'https://your-instance.example.com/api');
```
