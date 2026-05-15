# Moonwatch Codex Guide

This reference is the portable Codex version of the Moonwatch skill. Keep `SKILL.md` files short and read this reference only when the task needs details.

## Invocation

Codex does not currently expose Moonwatch as custom `/moonwatch-*` slash commands. Ask for the workflow in natural language or mention the skill explicitly:

- `set up Moonwatch for this project`
- `use $moonwatch:moonwatch-setup`
- `use $moonwatch:moonwatch-analyze-logs`
- `use $moonwatch:moonwatch-list-watchers`
- `use $moonwatch:moonwatch-monitoring`

## CLI

All Moonwatch reads and management operations go through the latest `@moonwatch/cli` package via `npx`. Do not check for, install, or use a global CLI binary.

Command prefix:

```bash
npx -y @moonwatch/cli@latest
```

Auth order:

1. `--key`
2. `MOONWATCH_PERSONAL_KEY`
3. `~/.config/moonwatch/config.json`

Exit codes: `0` ok, `1` API/server error, `2` bad usage/config, `3` auth failed, `4` rate-limited.

Common commands:

```bash
npx -y @moonwatch/cli@latest whoami
npx -y @moonwatch/cli@latest logs files --owned
npx -y @moonwatch/cli@latest logs files create <name> --workspace <workspaceId>
npx -y @moonwatch/cli@latest logs key <logFileId>
npx -y @moonwatch/cli@latest logs query <logFileId> "<SQL>"
npx -y @moonwatch/cli@latest watchers list --log <logFileId>
npx -y @moonwatch/cli@latest watchers get <watcherId>
npx -y @moonwatch/cli@latest watchers create --title "<title>" --description "<description>" --log <logFileId>
npx -y @moonwatch/cli@latest watchers update <watcherId> --findings "<findings>" --status-summary "<summary>"
npx -y @moonwatch/cli@latest monitoring projects list
npx -y @moonwatch/cli@latest monitoring dashboards list --project <projectId>
npx -y @moonwatch/cli@latest monitoring metrics --project <projectId>
npx -y @moonwatch/cli@latest monitoring query <projectId> "<SQL>"
npx -y @moonwatch/cli@latest monitoring cards list --dashboard <dashboardId>
```

## Log File Selection

Before querying logs, creating watchers, or listing watchers, resolve a log file:

1. Use `MOONWATCH_LOG_FILE_ID` by default.
2. Use `MOONWATCH_LOG_FILE_ID_DEV` only for local/dev requests.
3. If the env vars are missing, read project-local files in this order:
   - `.env.local` for developer-local values such as `MOONWATCH_LOG_FILE_ID_DEV`
   - `.codex/config.toml` for shared project values under `shell_environment_policy.set`
4. If no log file can be resolved, run the setup workflow.

Always pass the resolved log file ID explicitly.

## Watcher Investigations

Create a watcher before investigating runtime bugs:

```bash
npx -y @moonwatch/cli@latest watchers create \
  --title "500s on POST /api/orders" \
  --description "Investigating intermittent 500s on POST /api/orders. Initial suspicion: payment gateway timeout path lacks request timeout. Expected outcome: watcher logs should show gateway timing and retry behavior." \
  --log <logFileId>
```

Use the raw UUID without the `watcher-` prefix in SDK code and SQL queries.

Place temporary diagnostic logs with the watcher ID:

```ts
const dbg = logger.withWatcher("a1b2c3d4-...").withGroup("api/orders");
dbg.debug("create order entry", { orderId, itemCount: items.length });
dbg.debug("gateway call started", { orderId, timeoutMs });
dbg.debug("gateway call finished", { orderId, status, ms });
```

After instrumentation, update the watcher:

```bash
npx -y @moonwatch/cli@latest watchers update <watcherId> \
  --description "Investigating intermittent 500s on POST /api/orders. Instrumented src/services/orderService.ts for order entry, gateway timing, retry branch, and error handler. All logs use group api/orders. Expected outcome: gateway calls over 10s should correlate with 500s." \
  --status-summary "Debug logs placed. Awaiting reproduction."
```

Query watcher logs:

```sql
SELECT timestamp, group, message, metadata
FROM logs.entries
WHERE watcher_id = 'a1b2c3d4-...'
ORDER BY timestamp
```

Also query surrounding logs by timestamp to catch related system activity.

Cleanup rule: remove every temporary `logger.debug()`, `withWatcher()` setup, and logging import added solely for the investigation before resolving the watcher.

## Log Query Examples

```sql
SELECT timestamp, level, message, stack
FROM logs.entries
WHERE level = 'ERROR'
ORDER BY timestamp DESC
LIMIT 20
```

```sql
SELECT error_type, count() AS cnt
FROM logs.entries
WHERE error_type IS NOT NULL
GROUP BY error_type
ORDER BY cnt DESC
```

```sql
SELECT timestamp, level, group, message
FROM logs.entries
WHERE message LIKE '%ECONNREFUSED%'
ORDER BY timestamp DESC
LIMIT 20
```

```sql
SELECT toStartOfMinute(timestamp) AS minute, count()
FROM logs.entries
WHERE level = 'ERROR'
GROUP BY minute
ORDER BY minute
```

```sql
SELECT timestamp, group, level, message
FROM logs.entries
WHERE trace_id = '<traceId>'
ORDER BY timestamp
```

Include `source_context` when investigating production errors with source maps:

```sql
SELECT timestamp, message, stack, source_context
FROM logs.entries
WHERE level = 'ERROR' AND source_context IS NOT NULL
ORDER BY timestamp DESC
LIMIT 5
```

## SDK Logging

Look for an existing shared logger before creating a new one. Projects commonly expose `logger` or `getLogger()` from `logger.ts` or `lib/logger.ts`.

```ts
logger.debug("cache hit", { key: "user:42" });
logger.info("request handled", { method: "GET", path: "/api/users", ms: 12 });
logger.warn("slow query", { duration: 1200 });
logger.error(err as Error, { orderId: order.id });
```

Use groups:

```ts
const log = logger.withGroup("api/billing");
log.info("charge initiated", { amount, customerId });
```

Use `flush: "immediate"` for serverless/short-lived runtimes, `flush: "auto"` for long-running services, and `flush: "manual"` only when the caller controls flushing.

## Setup Workflow

1. Use `npx -y @moonwatch/cli@latest` for all Moonwatch CLI commands.
2. Verify `MOONWATCH_PERSONAL_KEY` by running `npx -y @moonwatch/cli@latest whoami`.
3. If missing or stale, ask the user for their personal key from `https://moonwatch.dev/app/setup`.
4. Prefer saving personal CLI auth to `~/.config/moonwatch/config.json` or instruct the user to export `MOONWATCH_PERSONAL_KEY`.
5. Run `npx -y @moonwatch/cli@latest logs files --owned` and select or create a production log file.
6. Save the shared production log ID in project-local Codex config so future Codex sessions inherit it. Create or update `.codex/config.toml`:
   ```toml
   [shell_environment_policy]
   set = { MOONWATCH_LOG_FILE_ID = "<production-log-id>" }
   ```
   If the project already has `[shell_environment_policy]`, merge the `set` values instead of replacing existing config. This file can be committed when it contains only non-secret shared project IDs.
7. Save developer-local log IDs in `.env.local`, which must remain uncommitted:
   ```bash
   MOONWATCH_LOG_FILE_ID_DEV=<developer-local-log-id>
   ```
   Use this for each developer's personal Moonwatch project during local development.
8. Also save project log IDs in the app's local environment file when the application itself needs them.
9. For SDK ingestion, fetch the workspace key with `npx -y @moonwatch/cli@latest logs key <logFileId>` when the user's role allows it, and store it as `MOONWATCH_API_KEY` in a local, uncommitted environment file. Do not commit workspace ingestion keys.

Do not ask the user for the workspace ingestion key unless `npx -y @moonwatch/cli@latest logs key` fails for permissions. The personal key and workspace key are different.

## Analyze Logs Workflow

1. Resolve the log file.
2. Query recent errors, error counts by type, volume by group, and any user-provided search term.
3. Check existing watchers with `npx -y @moonwatch/cli@latest watchers list --log <logFileId>`.
4. If a related watcher exists, read it and append new findings.
5. If none exists and ongoing monitoring would help, offer to create a watcher and instrument the relevant code.

Report the concrete pattern, affected endpoints/groups, timestamps, likely root cause, and next action.

## List Watchers Workflow

```bash
npx -y @moonwatch/cli@latest watchers list --log <logFileId>
npx -y @moonwatch/cli@latest watchers get <watcherId>
```

For watchers with new data, read findings first, query watcher logs, update `--findings`, and refresh `--status-summary`.

## Monitoring

Monitoring projects contain polls, events, snapshots, dashboards, and cards.

```bash
npx -y @moonwatch/cli@latest monitoring projects list
npx -y @moonwatch/cli@latest monitoring dashboards list --project <projectId>
npx -y @moonwatch/cli@latest monitoring metrics --project <projectId>
npx -y @moonwatch/cli@latest monitoring query <projectId> "<SQL>"
npx -y @moonwatch/cli@latest monitoring cards create --dashboard <dashboardId> --type graph --title "P95 latency" --config '<json>' --width 6
```

Useful queries:

```sql
SELECT timestamp, value
FROM monitoring.polls_realtime
WHERE name = 'api.latency.p95'
ORDER BY timestamp DESC
LIMIT 100
```

```sql
SELECT toStartOfMinute(timestamp) AS minute, count()
FROM monitoring.events
WHERE name = 'deploy'
GROUP BY minute
ORDER BY minute
```

When adding monitoring to code, use the existing Moonwatch SDK setup and choose names that are stable, queryable, and domain-specific.
