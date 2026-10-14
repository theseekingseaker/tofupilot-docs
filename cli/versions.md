<!-- Source: https://www.tofupilot.com/docs/cli/versions -->
# tofupilot versions
URL: /docs/cli/versions

Tag and roll out specific TofuPilot procedure versions from the terminal so factory stations only run the deployment you intend.

***

title: tofupilot versions
description: "Tag and roll out specific TofuPilot procedure versions from the terminal so factory stations only run the deployment you intend."
sidebarTitleCode: tofupilot versions
------------------------------------

The `versions` command manages procedure version tags.

## `get`

The `get` subcommand fetches a version, and the required flags are `--procedure-id` and `--tag`.

```bash title="get version"
tofupilot versions get --procedure-id proc_abc --tag v1.0.0
```

## `create`

The `create` subcommand creates a version tag, and the required flags are `--procedure-id` and `--tag`.

```bash title="create version"
tofupilot versions create --procedure-id proc_abc --tag v1.0.0
```

## `rm`

The `rm` subcommand deletes a version, and the required flags are `--procedure-id` and `--tag`.

```bash title="delete version"
tofupilot versions rm --procedure-id proc_abc --tag v1.0.0
```
