---
name: moonwatch-setup
description: Set up Moonwatch for Codex or a project. Use when the user asks to configure Moonwatch, install the `mw` CLI, authenticate with a personal key, choose log files, fetch ingestion keys, or add Moonwatch SDK logging.
---

# Moonwatch Setup

Follow `../moonwatch/references/moonwatch-guide.md#setup-workflow`.

Key points:

- Check `command -v mw` first.
- If `mw` is missing, ask once to run `npm install -g @moonwatch/cli`.
- If global install fails or is declined, use `npx -y @moonwatch/cli@latest`.
- Verify auth with `mw whoami`.
- Do not store personal keys in committed files.
- Store workspace ingestion keys only in local, uncommitted environment files.
- Prefer reusing an existing logger setup before adding SDK initialization.
