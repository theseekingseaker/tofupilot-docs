<!-- Source: https://www.tofupilot.com/docs/self-hosting/configure -->
# Configure
URL: /docs/self-hosting/configure

Learn how to configure domains, SSL, authentication providers, and the Git provider for your self-hosted TofuPilot instance via the .env file.

***

title: Configure
description: "Learn how to configure domains, SSL, authentication providers, and the Git provider for your self-hosted TofuPilot instance via the .env file."
-------------------------------------------------------------------------------------------------------------------------------------------------------------

import { Callout } from 'fumadocs-ui/components/callout';
import { DynamicCodeBlock } from '@/components/themed-dynamic-code-block';
import { Tabs, Tab } from 'fumadocs-ui/components/tabs';

Configuration lives in a single `.env` file that is generated on first run. To create it, SSH into your server and run the deploy command from Orbit:

<DynamicCodeBlock lang="bash" code="curl -fsSL https://www.tofupilot.com/deploy | bash -s -- --license YOUR_LICENSE_KEY" />

The script writes a `.env` template into the current directory and exits. Open it with your editor of choice:

<DynamicCodeBlock lang="bash" code="nano .env" />

<Callout type="info">
  The `.env` file also holds auto-generated secrets (`AUTH_SECRET`, `DB_PASSWORD`, `S3_SECRET_ACCESS_KEY`, the `CENTRIFUGO_*` keys, and `DEPLOYER_TOKEN`). Do not delete or modify them. They are created on first deploy and reused on every re-run.
</Callout>

## Domains

TofuPilot needs three subdomains. Create a DNS A record for each pointing to your server, then set them in `.env`:

* `DOMAIN_NAME` for the application (e.g. `tofupilot.yourcompany.com`).
* `STORAGE_DOMAIN_NAME` for file storage (e.g. `storage.tofupilot.yourcompany.com`).
* `CENTRIFUGO_DOMAIN_NAME` for realtime updates over WebSocket (e.g. `realtime.tofupilot.yourcompany.com`). Defaults to `realtime.<DOMAIN_NAME>` if unset.

Realtime features (live station status and telemetry, dashboard updates, and streaming build logs) need the `CENTRIFUGO_DOMAIN_NAME` record and its TLS certificate. Without it the app still works, but those views fall back to manual refresh.

## SSL certificates

HTTPS is handled by **Let's Encrypt** by default. To enable it, set your email:

<DynamicCodeBlock
  lang="bash"
  code="SSL_MODE=letsencrypt
SSL_LETSENCRYPT_EMAIL=admin@yourcompany.com"
/>

If you use your own certificate authority, switch to **custom** mode and provide absolute paths on the host:

<DynamicCodeBlock
  lang="bash"
  code="SSL_MODE=custom
SSL_CERT_PATH=/etc/ssl/tofupilot/fullchain.pem
SSL_KEY_PATH=/etc/ssl/tofupilot/privkey.pem"
/>

## Authentication

You need at least one sign-in method. TofuPilot supports email magic links and several OAuth providers, and you can enable as many as you want.

When you skip email, users sign in only through OAuth, which means no invitations or notifications.

### Email (magic link)

The email method sends a one-time code by email and drives invitations and notifications.

<DynamicCodeBlock
  lang="bash"
  code="EMAIL_SMTP_HOST=smtp.yourcompany.com
EMAIL_SMTP_PORT=587
EMAIL_SMTP_USER=noreply@yourcompany.com
EMAIL_SMTP_PASSWORD=your-smtp-password
EMAIL_FROM_AUTH=noreply@yourcompany.com"
/>

### Google

Create an OAuth app in the [Google Cloud Console](https://console.cloud.google.com/apis/credentials) and set the redirect URI to:

<DynamicCodeBlock lang="bash" code="https://tofupilot.yourcompany.com/api/auth/callback/google" />

Copy the Client ID and Client Secret into `.env`:

<DynamicCodeBlock
  lang="bash"
  code="AUTH_GOOGLE_ID=your-client-id
AUTH_GOOGLE_SECRET=your-client-secret"
/>

### Microsoft Entra ID

Register an app in the [Azure Portal](https://portal.azure.com) under App registrations and set the redirect URI to:

<DynamicCodeBlock lang="bash" code="https://tofupilot.yourcompany.com/api/auth/callback/microsoft" />

Copy the Application (client) ID and Secret into `.env`:

<DynamicCodeBlock
  lang="bash"
  code="AUTH_MICROSOFT_ENTRA_ID_ID=your-client-id
AUTH_MICROSOFT_ENTRA_ID_SECRET=your-client-secret
AUTH_MICROSOFT_ENTRA_ID_ISSUER=https://login.microsoftonline.com/your-tenant-id/v2.0"
/>

Replace `your-tenant-id` with your Azure AD tenant ID. For Azure Government, use `login.microsoftonline.us`.

### GitHub

Create an OAuth app in [GitHub Developer Settings](https://github.com/settings/developers) and set the callback URL to:

<DynamicCodeBlock lang="bash" code="https://tofupilot.yourcompany.com/api/auth/callback/github" />

Copy the Client ID and Client Secret into `.env`:

<DynamicCodeBlock
  lang="bash"
  code="AUTH_GITHUB_ID=your-client-id
AUTH_GITHUB_SECRET=your-client-secret"
/>

### GitLab

Create an application in [GitLab](https://gitlab.com/-/user_settings/applications) (or your self-hosted GitLab instance) with the `read_user` and `openid` scopes, and set the redirect URI to:

<DynamicCodeBlock lang="bash" code="https://tofupilot.yourcompany.com/api/auth/callback/gitlab" />

Copy the Application ID and Secret into `.env`:

<DynamicCodeBlock
  lang="bash"
  code="AUTH_GITLAB_ID=your-application-id
AUTH_GITLAB_SECRET=your-application-secret
AUTH_GITLAB_ISSUER=https://gitlab.com"
/>

For self-hosted GitLab, replace `https://gitlab.com` with your instance URL.

### Atlassian

Create an OAuth 2.0 (3LO) app in the [Atlassian developer console](https://developer.atlassian.com/console/myapps/), enable the User identity API with the `read:me` scope, and set the callback URL to:

<DynamicCodeBlock lang="bash" code="https://tofupilot.yourcompany.com/api/auth/callback/atlassian" />

Copy the Client ID and Secret into `.env`:

<DynamicCodeBlock
  lang="bash"
  code="AUTH_ATLASSIAN_ID=your-client-id
AUTH_ATLASSIAN_SECRET=your-client-secret"
/>

## Git provider

TofuPilot can connect to GitHub, GitLab, or Bitbucket Data Center so you can drive automatic [Deployments](/docs/deployments) from your CI/CD pipelines.

### GitHub

[Self-hosted](/docs/self-hosting) instances need their own [GitHub App](https://docs.github.com/en/apps).

**Step 1.** Go to your GitHub organization **Settings > Developer Settings > GitHub Apps > New GitHub App** and fill in:

| Field            | Value                                                       |
| ---------------- | ----------------------------------------------------------- |
| **App name**     | `tofupilot-yourcompany` (must be globally unique on GitHub) |
| **Homepage URL** | `https://DOMAIN_NAME`                                       |

**Step 2.** In **Identifying and authorizing users**, leave the Callback URL empty. Uncheck **Request user authorization (OAuth) during installation** and **Expire user authorization tokens**.

**Step 3.** Fill in the remaining sections:

* **Post installation**
  * Setup URL: `https://DOMAIN_NAME/api/github/setup`
  * Redirect on update: unchecked
* **Webhook**
  * Active: checked
  * Webhook URL: `https://DOMAIN_NAME/api/webhooks/github`
  * Secret: a random string (e.g. run `openssl rand -hex 32` in your terminal)
* **Repository permissions**
  * Contents: Read-only (clone repositories and read commits)
  * Administration: **Read and write** (required to create a repository from a template)
  * Metadata: Read-only (automatically selected)
  * Pull requests: Read-only
* **Organization permissions**: none
* **Account permissions**: none
* **Subscribe to events**
  * Delete, Pull request, Push, Repository
* **Install target**
  * Only on this account

<Callout type="warn">
  **Administration: Read and write** is required for cloning starter templates. Without it, creating a procedure from a template fails with a `403`.
</Callout>

**Step 4.** Click **Create GitHub App**. GitHub may redirect you to your instance's sign-in page afterward. That is expected: the App is already created. On the App's General page, note the **App ID**.

<Callout type="warn">
  Make sure **Push** is checked under **Subscribe to events** (Step 3). Without it GitHub won't notify your instance of new commits and auto-deploy won't trigger.
</Callout>

**Step 5.** Scroll to **Private keys**, click **Generate a private key**, and base64-encode the downloaded file:

<Tabs groupId="base64" persist items={['Linux', 'macOS', 'Windows']} label="">
  <Tab value="Linux">
    <DynamicCodeBlock lang="bash" code="base64 -w 0 < downloaded_file.pem" />
  </Tab>

  <Tab value="macOS">
    <DynamicCodeBlock lang="bash" code="base64 -i downloaded_file.pem" />
  </Tab>

  <Tab value="Windows">
    <DynamicCodeBlock lang="powershell" code="[Convert]::ToBase64String([IO.File]::ReadAllBytes(&#x22;downloaded_file.pem&#x22;))" />
  </Tab>
</Tabs>

**Step 6.** Add these variables to `.env`:

<DynamicCodeBlock
  lang="bash"
  code="GITHUB_APP_ID=your-app-id
GITHUB_APP_PRIVATE_KEY=your-base64-encoded-private-key
GITHUB_APP_WEBHOOK_SECRET=your-webhook-secret
GITHUB_APP_SLUG=tofupilot-yourcompany"
/>

* `GITHUB_APP_ID` comes from Step 4 (App's General page).
* `GITHUB_APP_PRIVATE_KEY` comes from Step 5 (base64-encoded output).
* `GITHUB_APP_WEBHOOK_SECRET` comes from Step 3 (webhook secret).
* `GITHUB_APP_SLUG` is the last segment of your App URL: `https://github.com/apps/{slug}`.

**Step 7.** After [deploying](/docs/self-hosting/deploy), go to **Settings > GitHub** in your dashboard to install the App and select repositories.

### GitLab

GitLab needs no extra environment variables. Go to **Settings > GitLab** in your dashboard and click **Connect** to add your GitLab access token, instance URL, and select a group.

### Bitbucket

Bitbucket Data Center needs no extra environment variables. Go to **Settings > Bitbucket** in your dashboard and click **Connect** to add your instance URL and a project-scoped [HTTP access token](https://confluence.atlassian.com/bitbucketserver/http-access-tokens-939515499.html) with **Project admin** permission, then select a project. Bitbucket Cloud (bitbucket.org) is not supported yet.

## Deployments

The deployments pipeline builds procedure bundles from your Git repository and serves them to stations. It runs automatically on self-hosted installs, no extra `.env` configuration is required:

* The `tofupilot-deployer` container builds bundles in an isolated Docker-in-Docker sandbox.
* Artifacts are stored in a dedicated bucket on the bundled object storage; the dashboard mints short-lived presigned links for stations to download them.
* `DEPLOYER_TOKEN` and the `ARTIFACT_*` settings are generated and wired automatically.

<Callout type="warn">
  The deployer needs a **privileged** container to run its build sandbox (its own Docker-in-Docker daemon — it does not mount the host Docker socket). It will not start under **rootless Docker**; the rest of the stack is unaffected. Use a standard (root) Docker installation if you need the build pipeline. Because builds execute your procedure code inside this privileged worker, run self-hosted on a host you control and treat the build worker as part of your trust boundary.
</Callout>

<Callout type="warn">
  The bundled object storage (SeaweedFS) serves both the app and artifact buckets without S3 authentication, so anything reachable on `storage.<your-domain>` can read stored objects directly. Keep that hostname on a trusted network, or front it with your own access control, if your build bundles contain proprietary source. This applies to the existing app bucket too.
</Callout>

To deploy automatically when you push to a branch, configure the [GitHub App](#github) above. The push webhook triggers a build for procedures with auto-deploy enabled. Manual deploys from the dashboard work without it.
