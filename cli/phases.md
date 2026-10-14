<!-- Source: https://www.tofupilot.com/docs/cli/phases -->
# tofupilot phases
URL: /docs/cli/phases

List and inspect TofuPilot test phases from the terminal, with filters for outcome, duration, retries, and units.

***

title: tofupilot phases
description: "List and inspect TofuPilot test phases from the terminal, with filters for outcome, duration, retries, and units."
sidebarTitleCode: tofupilot phases
----------------------------------

The `phases` command queries [Phases](/docs/fundamentals/test-data-model#phase), the individual steps inside runs.

## `ls`

The `ls` subcommand lists and filters phases, scoped to a single procedure via `--procedure-id`. The full filter list is: `--procedure-id`, `--names`, `--outcomes`, `--started-after`, `--started-before`, `--exclude-retries`, `--run-outcomes`, `--ids`, `--duration-min`, `--duration-max`, `--deployment-ids`, `--procedure-versions`, `--environments`, `--operated-by-ids`, `--operated-by-names`, `--created-by-station-ids`, `--created-by-user-ids`, `--serial-numbers`, `--part-numbers`, `--revision-numbers`, `--batch-numbers`, `--samples`, `--sort-by`, `--sort-order`, `--limit`, and `--cursor`.

```bash title="filter phases"
tofupilot phases ls --procedure-id proc_abc --outcomes FAIL --exclude-retries true
```

## `get`

The `get` subcommand fetches a single phase by ID.

```bash title="get phase"
tofupilot phases get --id phase_abc123
```
