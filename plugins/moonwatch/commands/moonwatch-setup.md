---
description: Set up Moonwatch for the current project by installing or falling back for the mw CLI, authenticating, selecting log files, and writing local environment configuration.
---

# /moonwatch-setup

Configure Moonwatch for the current project.

## Arguments

- `personal_key`: optional `mw_personal_...` key to use instead of prompting
- `prod_log_id`: optional production log file ID
- `dev_log_id`: optional dev/local log file ID

## Preflight

1. Check `command -v mw`.
2. If `mw` is missing, ask once to run `npm install -g @moonwatch/cli`.
3. If install fails or is declined, use `npx -y @moonwatch/cli@latest` as the command prefix.
4. Run `mw whoami` using `MOONWATCH_PERSONAL_KEY`, `--key`, or `~/.config/moonwatch/config.json`.
5. If auth fails and no `personal_key` argument was supplied, ask the user for the personal key from `https://moonwatch.dev/app/setup`.

## Plan

Configure auth and project log IDs without committing secrets.

- Personal CLI auth should go in `~/.config/moonwatch/config.json` or the user's shell environment.
- Project log IDs can be written to a local env file.
- Workspace ingestion key must only be written to an uncommitted local env file.
- Never print full key values in the conversation or command summary.

## Commands

1. If a personal key is supplied or collected, save it to `~/.config/moonwatch/config.json` if the CLI supports that config shape; otherwise instruct the user to export `MOONWATCH_PERSONAL_KEY`.
2. Run `mw logs files --owned`.
3. Select an existing log file, create one with `mw logs files create <name> --workspace <workspaceId>`, or use the provided `prod_log_id`.
4. Optionally select or create a dev log file.
5. Create or update `.env.local` in the project root with names only in the summary:

```bash
MOONWATCH_LOG_FILE_ID=<production-log-id>
MOONWATCH_LOG_FILE_ID_DEV=<dev-log-id>
MOONWATCH_API_KEY=<workspace-ingestion-key>
```

6. Ensure `.env.local` is ignored by git. If not, add `.env.local` to `.gitignore`.
7. Fetch the workspace ingestion key with `mw logs key <production-log-id>` when the role allows it, then write it to `.env.local` as `MOONWATCH_API_KEY`.

## Verification

1. Run `mw whoami`.
2. Run `mw logs query <production-log-id> "SELECT count() FROM logs.entries LIMIT 1"`.
3. Confirm `.env.local` exists and contains the expected key names without printing values.
4. Confirm `.env.local` is ignored by git.

## Summary

Report:

- CLI mode: global `mw` or `npx -y @moonwatch/cli@latest`
- Auth status: configured or needs user action
- Production log file ID: show only the ID, not ingestion key
- Dev log file ID: show only if configured
- Local env file path
- SDK setup status, if inspected

## Next Steps

Suggest using `/moonwatch-analyze-logs`, `/moonwatch-list-watchers`, or adding SDK logging if the project is not instrumented yet.
