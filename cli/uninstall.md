<!-- Source: https://www.tofupilot.com/docs/cli/uninstall -->
# tofupilot uninstall
URL: /docs/cli/uninstall

Remove the TofuPilot CLI binary along with queued uploads, pulled deployments, and cached configuration in one command.

***

title: tofupilot uninstall
description: "Remove the TofuPilot CLI binary along with queued uploads, pulled deployments, and cached configuration in one command."
sidebarTitleCode: tofupilot uninstall
-------------------------------------

The `uninstall` command removes the CLI binary and, by default, every local data file, including queued uploads, pulled deployments, and cached config.

```bash title="uninstall"
tofupilot uninstall
```

## `--keep-data`

The `--keep-data` flag preserves run data, queued uploads, and pulled deployments, which is useful before reinstalling.

```bash title="keep data"
tofupilot uninstall --keep-data
```

## `--yes`

The `--yes` flag skips the confirmation prompt, so you can use it in CI.

```bash title="ci uninstall"
tofupilot uninstall --yes
```

## See also

* **[CLI](/docs/cli)**: the CLI in development mode.
* **[Station](/docs/station)**: the CLI in factory mode.
* **[REST API](/docs/api/rest-api)**: the HTTP surface behind every resource subcommand.
