<!-- Source: https://www.tofupilot.com/docs/cli/users -->
# tofupilot users
URL: /docs/cli/users

List and inspect members of your TofuPilot organization from the terminal, with JSON output for audit and automation pipelines.

***

title: tofupilot users
description: "List and inspect members of your TofuPilot organization from the terminal, with JSON output for audit and automation pipelines."
sidebarTitleCode: tofupilot users
---------------------------------

The `users` command manages users in the active organization.

## `ls`

The `ls` subcommand lists users in the active organization, and you can pass `--current true` to return only the authenticated user.

```bash title="list users"
tofupilot users ls
tofupilot users ls --current true
```
