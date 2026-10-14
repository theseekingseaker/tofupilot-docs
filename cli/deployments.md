<!-- Source: https://www.tofupilot.com/docs/cli/deployments -->
# tofupilot deployments
URL: /docs/cli/deployments

List and inspect TofuPilot deployments from the terminal, with filters for environment, build status, branch, and author.

***

title: tofupilot deployments
description: "List and inspect TofuPilot deployments from the terminal, with filters for environment, build status, branch, and author."
sidebarTitleCode: tofupilot deployments
---------------------------------------

The `deployments` command inspects [Deployments](/docs/deployments), the built procedure versions that stations pull and run.

## `ls`

The `ls` subcommand lists and filters deployments. The full filter list is: `--procedure-ids`, `--environments`, `--build-statuses`, `--pushed`, `--branch-names`, `--author-usernames`, `--deployed-after`, `--deployed-before`, `--search-query`, `--limit`, and `--cursor`.

```bash title="list deployments"
tofupilot deployments ls --procedure-ids proc_abc --environments production
```

## `get`

The `get` subcommand fetches a single deployment by ID.

```bash title="get deployment"
tofupilot deployments get --id dep_abc123
```
