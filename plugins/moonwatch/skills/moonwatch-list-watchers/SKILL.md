---
name: moonwatch-list-watchers
description: List, review, and update Moonwatch watchers. Use when the user asks about active investigations, watcher status, new watcher data, investigation findings, or monitoring a previously instrumented issue.
---

# List Moonwatch Watchers

Invoke this skill with `list Moonwatch watchers` or `use $moonwatch:moonwatch-list-watchers`. Codex does not currently expose this as `/moonwatch-list-watchers`.

Follow `../moonwatch/references/moonwatch-guide.md#list-watchers-workflow`.

Use `npx -y @moonwatch/cli@latest watchers list --log <logFileId>` after resolving the log file. For watchers with new data, read the watcher, query matching logs, update findings, and refresh the status summary.
