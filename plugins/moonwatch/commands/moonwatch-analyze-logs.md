---
description: Analyze Moonwatch logs for recent errors, anomalies, and runtime failure patterns in the current project.
---

# /moonwatch-analyze-logs

Analyze Moonwatch logs using the workflow in `../skills/moonwatch/references/moonwatch-guide.md`.

## Preflight

1. Resolve the CLI: use `mw` if available, otherwise `npx -y @moonwatch/cli@latest`.
2. Resolve `MOONWATCH_LOG_FILE_ID`, using `.env.local` if needed.
3. Stop and direct the user to `/moonwatch-setup` if no log file ID is configured.

## Plan

Query recent errors, error types, volume by group, relevant search terms from `$ARGUMENTS`, and active watchers.

## Commands

- `mw watchers list --log <logFileId>`
- `mw logs query <logFileId> "<SQL>"`

Use `--json` when parsing output programmatically.

## Verification

Confirm queries returned data or clearly state that the log file has no matching entries.

## Summary

Report the concrete patterns, timestamps, affected groups, likely cause, and recommended next action.

## Next Steps

Offer to create a watcher and add diagnostic logging when runtime data is insufficient.
