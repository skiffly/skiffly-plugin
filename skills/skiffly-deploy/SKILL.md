---
name: skiffly-deploy
description: Deploy the current project, a GitHub repo, a Docker image or a static site to Skiffly and return a live HTTPS URL; add databases with persistent volumes, environment variables and custom domains. Use when the user says "deploy this", "put it online", "host this on Skiffly", "add a Postgres/Redis database" or "connect my domain".
---

# Deploy on Skiffly

Skiffly hosts apps, APIs, workers, databases and static sites. The model is
**workspace → project → environment (`production`, …) → service → deployment**.
All actions go through the `skiffly` MCP server bundled with this plugin.

## Before you start

- The first tool call opens a browser sign-in (OAuth). If the tools fail with an auth error, ask the user to run `/mcp` → **skiffly** → **Authenticate**, then retry.
- Call `whoami` once to learn the user's workspaces. Do not guess IDs.
- Check `list-projects` / `list-services` before creating anything, so you reuse what exists instead of making duplicates.

## Pick the path

| What the user has | Tool |
|---|---|
| A page or small site made in this chat, or a folder of HTML/CSS/JS | `publish-site` (files in, URL out; no Git needed) |
| Code in a GitHub repo | `create-deployment` with `repo` (`owner/name`) and `branch` |
| A Docker image (`nginx:1.27`, `ghcr.io/…`) | `create-service` with `image` and `port` (it deploys right away) |
| Code only on disk, larger than a few files | offer to push it to GitHub first (`gh repo create --private --source . --push`), or use the Skiffly CLI: `npx skiffly up` ([CLI guide](https://skiffly.dev/docs/guides/cli)) |

## Deploy a repo

1. Confirm the repo and branch with the user. For a monorepo, also the `rootDirectory`.
2. `create-project` (or reuse one), then `create-deployment` with `repo`, `branch` and, if needed, `rootDirectory`, `dockerfilePath`, `variables`. The build system detects the stack; a root `Dockerfile` is used automatically.
3. The app must listen on `$PORT`. If it uses a fixed port, pass `port`.
4. Poll `get-status` until the deployment is `success`. If it is `failed` or `crashed`, switch to the **skiffly-debug** skill.
5. `generate-domain` and give the user the `https://….skiffly.cloud` URL.

Private repos need the Skiffly GitHub App: check `github-installations`; if the repo is missing, send the user the install link it returns.

## Add a database

1. `create-service` with an image and port:
   `postgres:16` (5432), `mysql:8` (3306), `redis:7` (6379), `mongo:7` (27017).
   Generate a strong password and pass it in `variables` (for example `POSTGRES_PASSWORD`).
2. `create-volume` at the data path: `/var/lib/postgresql/data`, `/var/lib/mysql`, `/data`, `/data/db`.
3. Other services reach it by its internal host (`internalHost` in `list-services`), for example
   `DATABASE_URL=postgres://postgres:<password>@postgres:5432/postgres`. Set it on the app with `set-variables`.
4. Offer `create-backup` once data matters; `list-backups` / `restore-backup` restore it.

## Variables, domains, scaling

- `set-variables` redeploys the affected services unless `skipDeploys: true`. `list-variables` returns secrets: show names, not values, unless the user asks.
- Custom domain: `generate-domain` with `domain: app.example.com` returns the CNAME and TXT records the user adds at their DNS provider; `domain-status` confirms it. TLS is automatic.
- `update-service` changes replicas, CPU, memory, health check path, start command, cron schedule and sleeping.

## Money

Skiffly is prepaid. `billing-account` shows the balance, plan limits and usage. When the balance runs out, services pause until the user tops up. Do not create a payment unless the user asks for one.

## Rules

- Ask before anything destructive: `delete-service`, `delete-project`, `delete-volume`, `delete-domain`, `delete-variables`, restoring a backup, restarting a database.
- Never print tokens or secret values into chat, files or commits.
- `PLAN_LIMIT` errors mean the plan does not allow that size or count. Tell the user; do not retry with the same values.
- Docs: `search-docs`, or https://skiffly.dev/docs.
