<!-- Source: https://www.tofupilot.com/docs/cli/deploy -->
# tofupilot deploy
URL: /docs/cli/deploy

Deploy a TofuPilot procedure from your machine: upload local source, build it in the cloud, and stream the build logs to your terminal.

***

title: tofupilot deploy
description: "Deploy a TofuPilot procedure from your machine: upload local source, build it in the cloud, and stream the build logs to your terminal."
sidebarTitleCode: tofupilot deploy
----------------------------------

import { Callout } from 'fumadocs-ui/components/callout';

The `deploy` command uploads the linked procedure's local source tree, builds it in the cloud, and streams the build logs to your terminal. By default it creates a preview [Deployment](/docs/deployments) — a build artifact you can push to stations later with no rebuild.

```bash title="deploy"
tofupilot deploy
```

Deploy targets Python procedures: the directory must be linked to a procedure (`tofupilot link`) and contain a `pyproject.toml` (a uv package). Files matched by `.gitignore` are not uploaded; `.git`, `venv`, and `__pycache__` are always excluded.

## Production

`--prod` targets production: once the build is ready, every station linked to the procedure picks it up between runs.

```bash title="production deploy"
tofupilot deploy --prod
```

`--target=production` and `--target=preview` are explicit equivalents. `--yes` skips the confirmation prompt for scripted use, and `--json` prints a single JSON result instead of streaming logs.

<Callout type="warn">
  A production deploy overrides an instant rollback: it ships to every station and resumes auto-push, the same as pushing from the dashboard. Use a preview deploy if you only want to build.
</Callout>

## Authentication

`deploy` needs a user login (`tofupilot login` in the browser). Station setup tokens cannot deploy code.

## Traceability

Deployments created this way carry no git commit. The uploaded tarball and its SHA-256 are stored as the audit record and shown on the deployment as "Uploaded from CLI". For production lines that require git traceability, keep using auto-push from the linked repository.
