<!-- Source: https://www.tofupilot.com/docs/cli/logs -->
# tofupilot logs
URL: /docs/cli/logs

List and inspect TofuPilot run logs from the terminal, with filters for level, source file, run, unit, and time window.

***

title: tofupilot logs
description: "List and inspect TofuPilot run logs from the terminal, with filters for level, source file, run, unit, and time window."
sidebarTitleCode: tofupilot logs
--------------------------------

The `logs` command queries [Logs](/docs/fundamentals/test-data-model#log) captured during runs.

## `ls`

The `ls` subcommand lists and filters logs. The full filter list is: `--procedure-ids`, `--search-query`, `--levels`, `--timestamp-after`, `--timestamp-before`, `--source-files`, `--run-ids`, `--run-outcomes`, `--procedure-versions`, `--deployment-ids`, `--environments`, `--serial-numbers`, `--part-numbers`, `--revision-numbers`, `--batch-numbers`, `--samples`, `--created-by-station-ids`, `--created-by-user-ids`, `--sort-by`, `--sort-order`, `--limit`, and `--cursor`.

```bash title="filter logs"
tofupilot logs ls --run-ids run_abc --levels ERROR --limit 50
```

## `get`

The `get` subcommand fetches a single log entry by ID.

```bash title="get log"
tofupilot logs get --id log_abc123
```
