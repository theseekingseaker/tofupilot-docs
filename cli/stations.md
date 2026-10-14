<!-- Source: https://www.tofupilot.com/docs/cli/stations -->
# tofupilot stations
URL: /docs/cli/stations

Provision, list, and decommission TofuPilot test stations from the terminal so you can scale your factory benches without leaving the CLI.

***

title: tofupilot stations
description: "Provision, list, and decommission TofuPilot test stations from the terminal so you can scale your factory benches without leaving the CLI."
sidebarTitleCode: tofupilot stations
------------------------------------

The `stations` command manages [Stations](/docs/station).

## `ls`

The `ls` subcommand lists stations. Filters are `--limit`, `--cursor`, `--search-query`, and `--procedure-ids`.

```bash title="list stations"
tofupilot stations ls --procedure-ids proc_abc
```

## `get`

The `get` subcommand fetches a station by ID.

```bash title="get station"
tofupilot stations get --id station_abc
```

## `get-current`

The `get-current` subcommand returns the station the CLI is logged in as, which is useful in scripts running on a station. It requires station authentication; calling it with a user API key returns Forbidden.

```bash title="current station"
tofupilot stations get-current
```

## `create`

The `create` subcommand creates a station. The required flag is `--name`, and `--procedure-id` is optional to link at creation time.

```bash title="create station"
tofupilot stations create --name "Test Bench #1" --procedure-id proc_abc
```

## `update`

The `update` subcommand updates a station by ID, and optional flags are `--name`, `--image-id`, and `--team-id`.

```bash title="rename station"
tofupilot stations update --id station_abc --name "Bench A"
```

## `rm`

The `rm` subcommand deletes a station by ID.

```bash title="delete station"
tofupilot stations rm --id station_abc
```
