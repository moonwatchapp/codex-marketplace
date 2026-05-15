---
name: moonwatch
description: AI-powered runtime debugging, log analysis, watcher investigations, and monitoring with Moonwatch. Use when Codex needs to inspect application logs, analyze errors, add diagnostic logging, manage watchers, configure Moonwatch, query metrics, or build monitoring dashboards with the latest `@moonwatch/cli` via npx.
---

# Moonwatch

Use Moonwatch to get runtime evidence instead of guessing from static code. Moonwatch operations must use the latest `@moonwatch/cli` through `npx -y @moonwatch/cli@latest`; do not ask to install a global CLI binary.

## Invocation

Codex does not currently expose Moonwatch as `/moonwatch-*` slash commands. Use natural language or an explicit skill mention:

- `set up Moonwatch for this project`
- `use $moonwatch:moonwatch-setup`
- `use $moonwatch:moonwatch-analyze-logs`
- `use $moonwatch:moonwatch-list-watchers`
- `use $moonwatch:moonwatch-monitoring`

## CLI Usage

Use this command prefix for every Moonwatch CLI command:

```bash
npx -y @moonwatch/cli@latest
```

For example, run `npx -y @moonwatch/cli@latest whoami` to verify auth.

## Required Context

Resolve the log file before querying logs or managing watchers:

- Prefer `MOONWATCH_LOG_FILE_ID` for production.
- Use `MOONWATCH_LOG_FILE_ID_DEV` only when the user explicitly mentions local/dev.
- If env vars are missing, check `.env.local` for developer-local values, then `.codex/config.toml` for shared `shell_environment_policy.set.MOONWATCH_LOG_FILE_ID`.
- If no log file is configured, use the setup workflow in `../moonwatch-setup/SKILL.md`.

For complete commands, watcher patterns, setup details, SDK usage, query examples, and monitoring card config, read `references/moonwatch-guide.md`.

## Investigation Workflow

1. Create or reuse a watcher for runtime bugs.
2. Place watcher-scoped `logger.debug()` logs around relevant code paths.
3. Ask the user to reproduce the issue when reproduction is needed.
4. Query watcher logs with `npx -y @moonwatch/cli@latest logs query <logFileId> "SELECT ... WHERE watcher_id = '<uuid>' ..."`.
5. Record findings with `npx -y @moonwatch/cli@latest watchers update`.
6. Fix the bug.
7. Remove temporary diagnostic logs before marking the watcher resolved.

Use structured groups with `withGroup()` and watcher IDs with `withWatcher()` so logs are easy to query. Avoid raw `console.log` for watcher investigations.
