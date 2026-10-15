<img src="assets/logo.svg" alt="Skiffly" width="64" height="64">

# Skiffly for Claude Code, Cursor and Codex

Deploy from your AI coding agent. Say "deploy this" and get a live HTTPS URL.

[Skiffly](https://skiffly.dev) hosts apps, APIs, workers, databases and static sites. With this plugin your agent can:

- build and deploy a GitHub repo, a Docker image or a folder of HTML;
- add Postgres, MySQL, Redis or MongoDB with a persistent volume and wire the connection string;
- set environment variables and connect your own domain with automatic HTTPS;
- read build and runtime logs, fix a failed deploy and redeploy;
- move an app and its data from another platform or a VPS.

## What's inside

- **Skiffly MCP server** (remote, `https://mcp.skiffly.dev/mcp`): 40+ tools for projects, services, deployments, logs, variables, domains, volumes, backups and billing. You sign in once in the browser (OAuth 2.1); no tokens to copy.
- **Skills**
  - `skiffly-deploy`: ship a repo, Docker image or static site; add databases, variables and custom domains.
  - `skiffly-debug`: find why a deployment failed or crashed, fix it and redeploy.
  - `skiffly-migrate`: move an app and its data from another platform or a VPS, then switch DNS.

## Try it

- "Deploy this repo to Skiffly and give me the URL."
- "Add a Postgres database to my Skiffly project and set DATABASE_URL on the app."
- "My last Skiffly deploy failed. Find out why and fix it."
- "Publish this page as a website."

## Install

### Claude Code

```
/plugin marketplace add skiffly/skiffly-plugin
/plugin install skiffly@skiffly
```

Then run `/mcp`, choose **skiffly → Authenticate** and allow access in the browser.

### Cursor

Add the MCP server in `~/.cursor/mcp.json` (a Cursor Marketplace listing is on the way):

```json
{ "mcpServers": { "skiffly": { "url": "https://mcp.skiffly.dev/mcp" } } }
```

Cursor shows the server as *needs authentication* in Settings → MCP; click **Login**.

### Codex

```
codex plugin marketplace add skiffly/skiffly-plugin
codex plugin add skiffly@skiffly
```

### Claude (web and desktop)

Settings → **Connectors** → **Add custom connector**, name it `Skiffly`, URL `https://mcp.skiffly.dev/mcp`.

## Requirements

A Skiffly account (sign up at https://skiffly.dev with GitHub or e-mail). Skiffly is prepaid and usage-based; new accounts start on the free plan. Deploying private GitHub repos needs the Skiffly GitHub App, which the agent links to when needed.

## Privacy and data

This plugin contains only instructions (skills) and the address of the Skiffly MCP server. It runs no local code and installs no packages.

- The agent talks to `https://mcp.skiffly.dev`, operated by Skiffly, and only after you sign in and approve access. Requests act on your Skiffly account with the scopes you granted (`read`, `write`).
- Data sent: what the agent passes to the tools you use, such as project and service names, repository names, environment variables you ask it to set, and site files you ask it to publish. Logs and settings of your services are returned to the agent.
- Outside Skiffly, the skills only use tools you already have and only when you ask: `skiffly-deploy` can push your code to your own GitHub repository with `git`/`gh` before deploying, and `skiffly-migrate` can dump a database from your old host (for example with `pg_dump`) to restore it on Skiffly.
- Skiffly keeps your projects, settings and variables for as long as your account has them; deleting a project removes them.
- The plugin sends nothing anywhere else. Access can be revoked at any time in the Skiffly dashboard: Settings → Developer → CLI sessions & connected apps.
- Privacy policy: https://skiffly.dev/privacy. Terms: https://skiffly.dev/terms.

## Support

- Docs: https://skiffly.dev/docs/guides/mcp
- E-mail: support@skiffly.dev
- Issues: https://github.com/skiffly/skiffly-plugin/issues

## License

MIT
