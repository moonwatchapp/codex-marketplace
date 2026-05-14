---
name: moonwatch-setup
description: Set up Moonwatch for Codex or a project. Use when the user asks to configure Moonwatch, install the `mw` CLI, authenticate with a personal key, choose log files, fetch ingestion keys, or add Moonwatch SDK logging.
---

# Moonwatch Setup

Invoke this skill with `set up Moonwatch for this project` or `use $moonwatch:moonwatch-setup`. Codex does not currently expose this as `/moonwatch-setup`.

Follow `../moonwatch/references/moonwatch-guide.md#setup-workflow`.

Key points:

- Check `command -v mw` first.
- If `mw` is missing, ask once to run `npm install -g @moonwatch/cli`.
- If global install fails or is declined, use `npx -y @moonwatch/cli@latest`.
- Verify auth with `mw whoami`.
- Do not store personal keys in committed files.
- Store the shared production log file ID in project-local `.codex/config.toml` under `[shell_environment_policy].set`.
- Store developer-local log file IDs, such as `MOONWATCH_LOG_FILE_ID_DEV`, in uncommitted `.env.local`.
- Store workspace ingestion keys only in local, uncommitted environment files.
- Prefer reusing an existing logger setup before adding SDK initialization.
