# Skiffly plugin for Claude Code, Cursor and Codex

![Skiffly](assets/logo.svg)

Deploy apps, APIs, workers, databases and static sites to [Skiffly](https://skiffly.dev) straight from your AI coding agent. Ask "deploy this", and the agent creates the project, builds your GitHub repo or Docker image, adds a Postgres or Redis database with a persistent volume, sets environment variables, connects your domain and hands you a live HTTPS URL. When a deploy fails, it reads the build and runtime logs and fixes the cause.

## What's inside

- **Skiffly MCP server** (remote, `https://mcp.skiffly.dev/mcp`): 40+ tools for projects, services, deployments, logs, variables, domains, volumes, backups and billing. You sign in once in the browser (OAuth 2.1); no tokens to copy.
- **Skills**
  - `skiffly-deploy`: ship a repo, Docker image or static site; add databases, variables and custom domains.
  - `skiffly-debug`: find why a deployment failed or crashed, fix it and redeploy.
  - `skiffly-migrate`: move an app and its data from another platform or a VPS, then switch DNS.

## Install

### Claude Code

```
/plugin marketplace add skiffly/skiffly-plugin
/plugin install skiffly@skiffly
```

Then run `/mcp`, choose **skiffly → Authenticate** and allow access in the browser.

### Cursor

Install **Skiffly** from the Cursor Marketplace, or add the MCP server by hand in `~/.cursor/mcp.json`:

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

## Try it

- "Deploy this repo to Skiffly and give me the URL."
- "Add a Postgres database to my Skiffly project and set DATABASE_URL on the app."
- "My last Skiffly deploy failed. Find out why and fix it."
- "Publish this page as a website."

## Requirements

A Skiffly account (sign up at https://skiffly.dev with GitHub or e-mail). Skiffly is prepaid and usage-based; new accounts start on the free plan. Deploying private GitHub repos needs the Skiffly GitHub App, which the agent links to when needed.

## Privacy and data

This plugin contains only instructions (skills) and the address of the Skiffly MCP server. It runs no local code and installs no packages.

- The agent talks to `https://mcp.skiffly.dev`, operated by Skiffly, and only after you sign in and approve access. Requests act on your Skiffly account with the scopes you granted (`read`, `write`).
- Data sent: what the agent passes to the tools you use, such as project and service names, repository names, environment variables you ask it to set, and site files you ask it to publish. Logs and settings of your services are returned to the agent.
- Nothing is sent anywhere else. Access can be revoked at any time in the Skiffly dashboard: Settings → Developer → CLI sessions & connected apps.
- Privacy policy: https://skiffly.dev/privacy. Terms: https://skiffly.dev/terms.

## Support

- Docs: https://skiffly.dev/docs/guides/mcp
- E-mail: support@skiffly.dev
- Issues: https://github.com/skiffly/skiffly-plugin/issues

## License

MIT
