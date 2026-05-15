---
name: moonwatch-monitoring
description: Set up and manage Moonwatch monitoring projects, dashboards, cards, metrics, polls, events, snapshots, and monitoring SQL queries. Use when the user asks about dashboards, metrics, system health, charts, or long-running operational monitoring.
---

# Moonwatch Monitoring

Invoke this skill with `set up Moonwatch monitoring` or `use $moonwatch:moonwatch-monitoring`. Codex does not currently expose this as `/moonwatch-monitoring`.

Follow `../moonwatch/references/moonwatch-guide.md#monitoring`.

Use `npx -y @moonwatch/cli@latest monitoring projects list`, `npx -y @moonwatch/cli@latest monitoring dashboards list`, `npx -y @moonwatch/cli@latest monitoring metrics`, `npx -y @moonwatch/cli@latest monitoring query`, and `npx -y @moonwatch/cli@latest monitoring cards` commands. Prefer stable metric/event names and dashboard cards that answer operational questions directly.
