---
name: moonwatch-setup
description: Set up Moonwatch for Codex or a project. Use when the user asks to configure Moonwatch, authenticate with a personal key, choose log files, fetch ingestion keys, or add Moonwatch SDK logging.
---

# Moonwatch Setup

Invoke this skill with `set up Moonwatch for this project` or `use $moonwatch:moonwatch-setup`. Codex does not currently expose this as `/moonwatch-setup`.

Follow `../moonwatch/references/moonwatch-guide.md#setup-workflow`.

Key points:

- Use `npx -y @moonwatch/cli@latest` for every Moonwatch CLI command.
- Do not check for, install, or use a global CLI binary.
- Verify auth with `npx -y @moonwatch/cli@latest whoami`.
- Do not store personal keys in committed files.
- Store the shared production log file ID in project-local `.codex/config.toml` under `[shell_environment_policy].set`.
- Store developer-local log file IDs, such as `MOONWATCH_LOG_FILE_ID_DEV`, in uncommitted `.env.local`.
- Store workspace ingestion keys only in local, uncommitted environment files.
- Prefer reusing an existing logger setup before adding SDK initialization.
