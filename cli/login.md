<!-- Source: https://www.tofupilot.com/docs/cli/login -->
# tofupilot login
URL: /docs/cli/login

Authenticate the TofuPilot CLI through the browser as a user, or with a headless setup token to register an unattended factory station.

***

title: tofupilot login
description: "Authenticate the TofuPilot CLI through the browser as a user, or with a headless setup token to register an unattended factory station."
sidebarTitleCode: tofupilot login
---------------------------------

The `login` command authenticates the CLI. The default flow opens a browser to log in as a user; the `--token` flag switches to a headless setup token that registers the machine as a station.

```bash title="login"
tofupilot login
```

## `--url <URL>`

Override the dashboard URL, which defaults to `https://www.tofupilot.app`, so you can log in against a [self-hosted](/docs/self-hosting) dashboard.

```bash title="self-hosted login"
tofupilot login --url https://tofupilot.acme.internal
```

## `--org <SLUG>`

When you already know the organization slug, pass `--org` to skip interactive organization selection.

```bash title="org login"
tofupilot login --org acme
```

## `--token <TOKEN>`

To authenticate as a station with a one-hour setup token issued by the dashboard, pass `--token`. The flow is headless, so no browser is required, and the flag conflicts with `--org`.

```bash title="headless station install"
tofupilot login --token <setup-token-from-dashboard>
```
