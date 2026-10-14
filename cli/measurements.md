<!-- Source: https://www.tofupilot.com/docs/cli/measurements -->
# tofupilot measurements
URL: /docs/cli/measurements

List and inspect TofuPilot measurements from the terminal, with filters for value ranges, outcomes, phases, and units.

***

title: tofupilot measurements
description: "List and inspect TofuPilot measurements from the terminal, with filters for value ranges, outcomes, phases, and units."
sidebarTitleCode: tofupilot measurements
----------------------------------------

The `measurements` command queries [Measurements](/docs/fundamentals/test-data-model#measurement) recorded inside phases.

## `ls`

The `ls` subcommand lists and filters measurements, scoped to a single procedure via `--procedure-id`. The full filter list is: `--procedure-id`, `--phase-name`, `--measurement-name`, `--phase-names`, `--outcomes`, `--ids`, `--duration-min`, `--duration-max`, `--deployment-ids`, `--procedure-versions`, `--environments`, `--serial-numbers`, `--part-numbers`, `--revision-numbers`, `--batch-numbers`, `--operated-by-ids`, `--operated-by-names`, `--created-by-station-ids`, `--created-by-user-ids`, `--started-after`, `--started-before`, `--exclude-retries`, `--value-min`, `--value-max`, `--value-bool`, `--value-strings`, `--measurement-outcomes`, `--measurement-types`, `--sort-by`, `--sort-order`, `--limit`, and `--cursor`.

```bash title="filter measurements"
tofupilot measurements ls --procedure-id proc_abc --measurement-name voltage --value-min 3.2
```

## `get`

The `get` subcommand fetches a single measurement by ID.

```bash title="get measurement"
tofupilot measurements get --id meas_abc123
```
