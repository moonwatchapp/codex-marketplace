---
description: Set up or manage Moonwatch monitoring projects, dashboards, cards, metrics, events, snapshots, and monitoring SQL queries.
---

# /moonwatch-monitoring

Manage Moonwatch monitoring using `../skills/moonwatch/references/moonwatch-guide.md`.

## Preflight

1. Resolve the CLI: use `mw` if available, otherwise `npx -y @moonwatch/cli@latest`.
2. Verify auth with `mw whoami`.
3. Inspect `$ARGUMENTS` to determine whether the user wants setup, query, dashboard/card work, or SDK instrumentation.

## Plan

List existing projects and dashboards before creating new resources. For dashboard changes, describe the intended cards and data sources before writing.

## Commands

- `mw monitoring projects list`
- `mw monitoring dashboards list --project <projectId>`
- `mw monitoring metrics --project <projectId>`
- `mw monitoring query <projectId> "<SQL>"`
- `mw monitoring cards list --dashboard <dashboardId>`
- `mw monitoring cards create --dashboard <dashboardId> --type graph|table|keyval --title "<title>" --config '<json>'`

## Verification

After creating or updating monitoring resources, list dashboards/cards again and query data if available.

## Summary

Report the project, dashboard, cards changed, and any SDK code changes.

## Next Steps

Suggest waiting for data to flow, then running `mw monitoring metrics --project <projectId>` and adding cards for discovered metrics.
