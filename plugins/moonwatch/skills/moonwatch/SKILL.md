---
name: moonwatch
description: AI-powered runtime debugging, log analysis, watcher investigations, and monitoring with Moonwatch. Use when Codex needs to inspect application logs, analyze errors, add diagnostic logging, manage watchers, configure Moonwatch, query metrics, or build monitoring dashboards with the `mw` CLI.
---

# Moonwatch

Use Moonwatch to get runtime evidence instead of guessing from static code. Moonwatch operations use the `mw` CLI from `@moonwatch/cli`; if `mw` is not available, install it once when possible and fall back to `npx`.

## Invocation

Codex does not currently expose Moonwatch as `/moonwatch-*` slash commands. Use natural language or an explicit skill mention:

- `set up Moonwatch for this project`
- `use $moonwatch:moonwatch-setup`
- `use $moonwatch:moonwatch-analyze-logs`
- `use $moonwatch:moonwatch-list-watchers`
- `use $moonwatch:moonwatch-monitoring`

## CLI Setup

Before the first Moonwatch command in a session:

1. Run `command -v mw`.
2. If missing, ask once whether to install globally with `npm install -g @moonwatch/cli`.
3. After installing, verify with `mw --version` or `mw whoami`.
4. If install fails or the user declines, use `npx -y @moonwatch/cli@latest` as the command prefix for every `mw` command.

Do not block Moonwatch usage just because global install is unavailable.

## Required Context

Resolve the log file before querying logs or managing watchers:

- Prefer `MOONWATCH_LOG_FILE_ID` for production.
- Use `MOONWATCH_LOG_FILE_ID_DEV` only when the user explicitly mentions local/dev.
- If no log file is configured, check project-local `.codex/config.toml` for `shell_environment_policy.set.MOONWATCH_LOG_FILE_ID`, then use the setup workflow in `../moonwatch-setup/SKILL.md`.

For complete commands, watcher patterns, setup details, SDK usage, query examples, and monitoring card config, read `references/moonwatch-guide.md`.

## Investigation Workflow

1. Create or reuse a watcher for runtime bugs.
2. Place watcher-scoped `logger.debug()` logs around relevant code paths.
3. Ask the user to reproduce the issue when reproduction is needed.
4. Query watcher logs with `mw logs query <logFileId> "SELECT ... WHERE watcher_id = '<uuid>' ..."`.
5. Record findings with `mw watchers update`.
6. Fix the bug.
7. Remove temporary diagnostic logs before marking the watcher resolved.

Use structured groups with `withGroup()` and watcher IDs with `withWatcher()` so logs are easy to query. Avoid raw `console.log` for watcher investigations.
