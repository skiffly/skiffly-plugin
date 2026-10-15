---
name: skiffly-debug
description: Diagnose and fix a failed, crashed or misbehaving Skiffly deployment by reading build, deploy and system logs, then changing code or service settings and redeploying. Use when a Skiffly deploy fails, the site returns 502/503, the app restarts, runs out of memory, or the user asks "why is my Skiffly app down?".
---

# Debug a Skiffly deployment

## 1. Find the failing deployment

- `get-status` for the service, or `list-deployments` and take the newest one with status `failed`, `crashed` or `removed`.
- `describe-service` shows the current settings (start command, port, health check, memory, root directory).

## 2. Read the right logs

`get-logs` with the deployment ID and `types`:

| Symptom | Log type | Usual cause |
|---|---|---|
| Never reached "deploying" | `build` | missing lockfile, wrong root directory, build script error, unsupported version |
| Deployed, then `crashed` | `deploy` | app exits on start, missing env variable, cannot reach the database |
| Restarts, `OOMKilled`, health check timeouts | `system` | memory limit too low, app not listening on `$PORT`, slow start |

Read the last error lines first, then scroll up to the first error: the first one is usually the real cause.

## 3. Fix

- **Code problem**: fix it in the repo, commit and push. If the service follows a branch, the push deploys; otherwise call `deploy`.
- **Settings problem**: `update-service` (`startCommand`, `rootDirectory`, `dockerfilePath`, `healthcheckPath`, `memoryMb`, `port`), then `redeploy`.
- **Missing variable**: `set-variables` (redeploys the service by default).
- **Port**: the app must bind `0.0.0.0:$PORT`. Binding `localhost` or a hard-coded port causes 502s.
- **Database connection refused**: use the internal host from `list-services`, not `localhost`; check the volume and that the database service is `success`.

## 4. Confirm

Poll `get-status` until `success`, then open the domain (or ask the user to) and check the response. If it fails again with a different error, repeat from step 2. After two failed attempts with the same error, stop and explain what you found instead of guessing.

## Rules

- Do not delete services, volumes or data to "start fresh" without asking.
- `restart-service` restarts in place without rebuilding; `redeploy` rebuilds the last commit; `cancel-deployment` stops a stuck build.
- Never paste secret variable values into chat.
