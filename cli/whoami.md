<!-- Source: https://www.tofupilot.com/docs/cli/whoami -->
# tofupilot whoami
URL: /docs/cli/whoami

Print the TofuPilot user or station currently authenticated on the CLI along with the active dashboard URL.

***

title: tofupilot whoami
description: "Print the TofuPilot user or station currently authenticated on the CLI along with the active dashboard URL."
sidebarTitleCode: tofupilot whoami
----------------------------------

The `whoami` command prints the currently logged-in user or station along with the active dashboard URL.

```bash title="whoami"
tofupilot whoami
```

With `--json`, the command prints a single object typed `"whoami"` with `auth_type` (`user` or `station`), `user_id`, `user_name`, `user_email`, `station_id`, `station_name`, `organization_name`, and `organization_slug`. The user fields are `null` for station auth, and vice versa.

```bash title="whoami json"
tofupilot whoami --json
```
