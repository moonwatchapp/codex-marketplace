---
name: moonwatch-analyze-logs
description: Analyze Moonwatch logs for errors, anomalies, runtime bugs, regressions, incidents, and production behavior. Use when the user asks why something failed, asks to inspect logs, or mentions recent errors in a running application.
---

# Analyze Moonwatch Logs

Invoke this skill with `analyze Moonwatch logs` or `use $moonwatch:moonwatch-analyze-logs`. Codex does not currently expose this as `/moonwatch-analyze-logs`.

Follow `../moonwatch/references/moonwatch-guide.md#analyze-logs-workflow`.

Always resolve the log file first. Query recent errors, error types, relevant groups, trace IDs, and user-provided terms. Check active watchers before creating a new one. Report concrete timestamps, patterns, and next steps.
