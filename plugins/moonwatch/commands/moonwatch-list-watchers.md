---
description: List and review Moonwatch watchers for the current project, including new data and investigation findings.
---

# /moonwatch-list-watchers

List and review Moonwatch watchers using `../skills/moonwatch/references/moonwatch-guide.md`.

## Preflight

1. Resolve the CLI: use `mw` if available, otherwise `npx -y @moonwatch/cli@latest`.
2. Resolve `MOONWATCH_LOG_FILE_ID`, using `.env.local` if needed.
3. Stop and direct the user to `/moonwatch-setup` if no log file ID is configured.

## Plan

Show active watchers first, highlight watchers with new data, and inspect new data when requested.

## Commands

- `mw watchers list --log <logFileId>`
- `mw watchers get <watcherId>`
- `mw logs query <logFileId> "SELECT timestamp, message, group, metadata FROM logs.entries WHERE watcher_id = '<uuid>' ORDER BY timestamp DESC LIMIT 50"`
- `mw watchers update <watcherId> --findings "<findings>" --status-summary "<summary>"`

## Verification

After updating a watcher, re-read it or list watchers to confirm the status summary changed.

## Summary

Report watcher titles, status summaries, new-data state, and any findings reviewed.

## Next Steps

Suggest querying watcher logs or resolving stale watchers after diagnostic logs are cleaned up.
