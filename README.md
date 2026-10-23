# Sveltia CMS Authenticator

This simple [Cloudflare Workers](https://workers.cloudflare.com/) script allows [Sveltia CMS](https://sveltiacms.app/en/) users to authenticate with [GitHub](https://docs.github.com/en/apps/oauth-apps/building-oauth-apps/authorizing-oauth-apps).

<!-- prettier-ignore-start -->
> [!IMPORTANT]
> **In most cases, you don’t need this authenticator**. Sveltia CMS supports multiple authentication methods for GitHub and GitLab, so you can choose the one that best suits your needs without having to set up this OAuth client.
<!-- prettier-ignore-end -->

<!-- prettier-ignore-start -->
> [!IMPORTANT]
> Although this authenticator is compatible with Netlify CMS (now Decap CMS), it is designed for use with modern Sveltia CMS instances. Please refer to the [Sveltia CMS documentation](https://sveltiacms.app/en/docs/successor-to-netlify-cms) for more information about the differences between these products and the reasons why Decap CMS should not be used for new projects.
<!-- prettier-ignore-end -->

## When to use it

### You don’t need it if…

- You’re migrating from Netlify CMS or Decap CMS
  - Your site is being deployed to Netlify
    - You can keep using Netlify’s built-in OAuth client for Sveltia CMS without any changes to your CMS configuration.
  - You already use [another 3rd party OAuth client](https://decapcms.org/docs/external-oauth-clients/)
    - You can keep using it for Sveltia CMS without any changes to your CMS configuration.
- You’re using GitHub
  - You or technical users are the only users of your CMS instance
    - Use the [access token method](https://sveltiacms.app/en/docs/backends/github#access-token) instead.
- You’re using GitLab
  - You or technical users are the only users of your CMS instance
    - Use the [access token method](https://sveltiacms.app/en/docs/backends/gitlab#access-token) instead.
  - Non-technical users need to sign into the CMS
    - Use [client-side PKCE authorization](https://sveltiacms.app/en/docs/backends/gitlab#pkce-authorization) instead.

### You may need it only if…

- You’re using GitHub
  - Non-technical users need to sign into the CMS
    - This authenticator will provide a better user experience than the access token method, which requires users to create and manage their own personal access tokens.

<!-- prettier-ignore-start -->
> [!NOTE]
> GitLab users don’t need this authenticator at all. GitLab support is included here only for completeness.
<!-- prettier-ignore-end -->

## How to use it

### Step 1. Deploy this project to Cloudflare Workers

Sign up with Cloudflare, and click the button below to start deploying.

[![Deploy to Cloudflare Workers](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/sveltia/sveltia-cms-auth)

Alternatively, you can clone the project and run [`wrangler deploy`](https://developers.cloudflare.com/workers/wrangler/commands/#deploy) locally.

Once deployed, open your Cloudflare Workers dashboard, select the `sveltia-cms-auth` service, then the worker URL (`https://sveltia-cms-auth.<SUBDOMAIN>.workers.dev`) will be displayed. Copy it for Step 2. It will also be used in Step 4.

### Step 2. Register the Worker as an OAuth app

#### GitHub

[Register a new OAuth application](https://github.com/settings/applications/new) on GitHub ([details](https://docs.github.com/en/apps/oauth-apps/building-oauth-apps/creating-an-oauth-app)) with the following properties, including your Worker URL from Step 1:

- Application name: `Sveltia CMS Authenticator` (or whatever)
- Homepage URL: `https://github.com/sveltia/sveltia-cms-auth` (or whatever)
- Application description: (can be left empty)
- Authorization callback URL: `<YOUR_WORKER_URL>/callback`

Once registered, click on the **Generate a new client secret** button. The app’s **Client ID** and **Client Secret** will be displayed. We’ll use them in Step 3 below.

#### GitLab

[Register a new OAuth application](https://gitlab.com/-/user_settings/applications) on GitLab ([details](https://docs.gitlab.com/ee/integration/oauth_provider.html#create-a-user-owned-application)) with the following properties, including your Worker URL from Step 1:

- Name: `Sveltia CMS Authenticator` (or whatever)
- Redirect URI: `<YOUR_WORKER_URL>/callback`
- Confidential: Yes
- Scopes: `api` only

Once registered, the app’s **Application ID** and **Secret** will be displayed. We’ll use them in Step 3 below.

### Step 3. Configure the Worker

Go back to the `sveltia-cms-auth` service page on the Cloudflare dashboard, select **Settings** > **Variables**, and add the following Environment Variables to your worker ([details](https://developers.cloudflare.com/workers/platform/environment-variables/#environment-variables-via-the-dashboard)):

#### GitHub

- `GITHUB_CLIENT_ID`: **Client ID** from Step 2
- `GITHUB_CLIENT_SECRET`: **Client Secret** from Step 2; click the **Encrypt** button to hide it
- `GITHUB_HOSTNAME`: Required only if you’re using GitHub Enterprise Server. Default: `github.com`

#### GitLab

- `GITLAB_CLIENT_ID`: **Application ID** from Step 2
- `GITLAB_CLIENT_SECRET`: **Secret** from Step 2; click the **Encrypt** button to hide it
- `GITLAB_HOSTNAME`: Required only if you’re using a self-hosted instance. Default: `gitlab.com`

#### Both GitHub and GitLab

- `ALLOWED_DOMAINS`: (Optional) Your site’s hostname, e.g. `www.example.com`
  - Multiple hostnames can be defined as a comma-separated list, e.g. `www.example.com, www.example.org`
  - A wildcard (`*`) can be used to match any subdomain, e.g. `*.example.com` that will match `www.example.com`, `blog.example.com`, `docs.api.example.com`, etc. (but not `example.com`)
  - To match a `www`-less naked domain and all the subdomains, use `example.com, *.example.com`
  - Hostnames are matched case-insensitively, so `www.Example.com` and `www.example.com` are equivalent
  - The list serves two purposes: it stops other sites from using your Worker at your expense (anti-abuse), and it stops them from obtaining an access token through it (security), because the authenticator releases a token only to a page served over HTTPS from one of these hostnames — a page on `localhost` is also accepted, so local development still works

<!-- prettier-ignore-start -->
> [!IMPORTANT]
> `ALLOWED_DOMAINS` is optional, but it’s both an anti-abuse and a security measure, so setting it is strongly recommended.
<!-- prettier-ignore-end -->

Save and deploy.

### Step 4. Update your CMS configuration

Open `admin/config.yml` locally or remotely, and add your Worker URL from Step 1 as the new `base_url` property under `backend`:

```diff
 backend:
   name: github # or gitlab
   repo: username/repo
   branch: main
+  base_url: <YOUR_WORKER_URL>
```

Commit the change. Once deployed, you can sign into Sveltia CMS remotely with GitHub or GitLab!

## Troubleshooting

When sign-in fails, the pop-up window shows the authenticator’s error message. The same message is embedded in the pop-up’s HTML together with a machine-readable `errorCode`, so you can still read it if the window closes before you can: open the Worker URL directly in a browser tab (see [Testing the Worker directly](#testing-the-worker-directly)) and view the page source with <kbd>Ctrl</kbd>+<kbd>U</kbd> (<kbd>Cmd</kbd>+<kbd>Option</kbd>+<kbd>U</kbd> on macOS). No developer tools needed.

### Error codes

| `errorCode` | What happened | How to fix it |
| --- | --- | --- |
| `MISCONFIGURED_CLIENT` | The Worker has no client ID or secret for the requested provider. | Add `GITHUB_CLIENT_ID` and `GITHUB_CLIENT_SECRET` (or the GitLab pair) as described in Step 3, then redeploy the Worker. Variables added in the dashboard only take effect after a deploy. |
| `UNSUPPORTED_DOMAIN` | The site’s hostname doesn’t match any entry in `ALLOWED_DOMAINS`. | See [`ALLOWED_DOMAINS` takes bare hostnames](#allowed_domains-takes-bare-hostnames) below. This is the most common setup mistake. |
| `UNSUPPORTED_BACKEND` | The `provider` parameter is neither `github` nor `gitlab`. | Check `backend.name` in your CMS configuration; it must be `github` or `gitlab`. |
| `AUTH_CODE_REQUEST_FAILED` | The provider sent the user back without an authorization code. | Usually the user clicked **Cancel** on the authorization screen. If it happens every time, make sure the OAuth app’s callback URL is exactly `<YOUR_WORKER_URL>/callback`. |
| `CSRF_DETECTED` | The `state` value returned by the provider doesn’t match the one stored in the cookie. | The cookie was lost: third-party cookies are blocked, the browser is in a strict privacy mode, or the flow was finished in a different window than it was started in. Allow cookies for the Worker’s domain and retry the whole flow in one window. |
| `TOKEN_REQUEST_FAILED` | The provider refused to exchange the authorization code for a token. | Almost always a wrong, rotated or truncated client secret. Generate a new one and update `GITHUB_CLIENT_SECRET` / `GITLAB_CLIENT_SECRET`, then redeploy. The provider’s own message is passed through, so read it in the page source. |
| `MALFORMED_RESPONSE` | The token endpoint answered with something that is neither a token nor an error. | Check `GITHUB_HOSTNAME` / `GITLAB_HOSTNAME`; for a self-hosted instance, a proxy or a sign-in page in front of it can answer in the provider’s place. The Worker logs the HTTP status it got — read it with `wrangler tail` or in the dashboard’s **Logs** tab. |

### Testing the Worker directly

You can start the flow without going through the CMS. Open this in a browser tab, replacing both placeholders with your own values:

```
https://sveltia-cms-auth.<SUBDOMAIN>.workers.dev/auth?provider=github&site_id=www.example.com
```

A working setup redirects straight to the provider’s authorization screen. Anything else renders the error page described above, where the `errorCode` tells you which row of the table applies.

### `ALLOWED_DOMAINS` takes bare hostnames

`ALLOWED_DOMAINS` is a list of **hostnames** — no scheme, no port, no path, no trailing slash. A value like `https://www.example.com/` is not a hostname, matches nothing, and makes every single sign-in fail with `UNSUPPORTED_DOMAIN`.

| Correct                        | Wrong                            |
| ------------------------------ | -------------------------------- |
| `www.example.com`              | `https://www.example.com/`       |
| `example.com, www.example.com` | `example.com/, www.example.com/` |
| `*.example.com`                | `https://*.example.com:443`      |

Two more things worth knowing when a hostname looks right but still gets rejected:

- The page asking for the token must be served over HTTPS, or be on `localhost` / `127.0.0.1`. A site still on plain HTTP is refused even if its hostname is allowed.
- Matching is case-insensitive, so the capitalization of your entries never matters.

## FAQ

### Why do I have to set this thing up in the first place?

Technically, we could host Sveltia CMS Authenticator on our own server and let anyone use it, just like Netlify does. The cost probably wouldn’t matter because it’s just a small, short-lived script. However, running such a **service** certainly comes with legal, privacy and security liabilities that we cannot afford. Remember that Sveltia CMS is nothing more than [@kyoshino](https://github.com/kyoshino)’s personal project. That’s why the authenticator is not offered as SaaS and you have to install it yourself.

## Acknowledgements

This project was inspired by [`netlify-cms-oauth-firebase`](https://github.com/Herohtar/netlify-cms-oauth-firebase).
