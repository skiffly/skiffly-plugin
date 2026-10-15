---
name: skiffly-migrate
description: Move an existing app from another hosting platform or a VPS to Skiffly - map its services, databases, environment variables, volumes and domains, recreate them on Skiffly, move the data and switch DNS with minimal downtime. Use when the user wants to migrate, move or switch their app or database to Skiffly.
---

# Migrate an app to Skiffly

## 1. Inventory

Ask for, or read from the repo and the current platform's config files (`Procfile`, `Dockerfile`, `docker-compose.yml`, `fly.toml`, `render.yaml`, `vercel.json`, `app.json`…):

- each service: repo and branch or Docker image, build and start commands, port, health check, cron schedule;
- databases and their versions, and any persistent disks;
- environment variables (names only in chat);
- custom domains and where DNS is managed.

Write the plan as a short table and confirm it with the user before creating anything.

## 2. Recreate on Skiffly

- One Skiffly project per app; one service per process.
- Create services with `deploy: false` (or `create-deployment` once settings are known) so nothing half-configured starts.
- Code services: `create-deployment` with `repo`, `branch`, `rootDirectory`, `startCommand`.
- Databases: `create-service` with the matching image and version, plus `create-volume` (see the **skiffly-deploy** skill for paths).
- Variables: `set-variables` with `skipDeploys: true` while you set everything up, then `deploy` once. Point `DATABASE_URL`-style variables at the new internal hosts.
- If the tool list has an `import_from_…` tool for the source platform, run it with `dryRun: true` first, show the plan, then apply it.

Platform-specific guides: https://skiffly.dev/docs/guides/migrate-from-heroku and the other migration guides in the docs.

## 3. Move the data

Data is never copied automatically. For Postgres, a typical path:

1. Put the old app in maintenance mode or stop writes.
2. `pg_dump --no-owner --format=custom` from the old database.
3. Restore into the new one with `pg_restore --no-owner` (through a temporary TCP access or from a one-off job service).
4. Compare row counts on the main tables.

For files on disks, copy them into the new volume the same way. Ask the user before any step that stops their production app.

## 4. Switch traffic

1. Test the app on its `*.skiffly.cloud` address first.
2. `generate-domain` with the custom domain; give the user the CNAME and TXT records.
3. Lower the DNS TTL beforehand if possible, switch the records, and watch `domain-status` until the certificate is issued.
4. Keep the old platform running until the user confirms everything works, then let them shut it down.

## Rules

- Never ask for or store passwords to the old platform in files; use tokens the user pastes once, and only for the call that needs them.
- Never delete anything on the old platform; that is the user's call.
