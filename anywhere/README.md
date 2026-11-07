# Craft Anything — anywhere (fork wrapper)

This fork adds a thin, host-agnostic **`anywhere/`** layer on top of the **original, unmodified**
[Craft Anything](https://github.com/storytold/craft-anything) engine. No upstream crate, app or
tool code is changed. The wrapper only **installs, connects and orchestrates** the upstream
CLI + MCP server so you can drop it into any AI host and use it to study and recreate shipped
software in Deep mode.

## What the wrapper adds

- `anywhere/SKILL.md` — a host-agnostic skill (`craft-anything-anywhere`) any agent can load.
- `anywhere/install.ps1` / `anywhere/install.sh` — one-command bootstrap:
  1. download the official release CLI (or reuse one on PATH),
  2. `craft-anything-cli setup --all-detected --yes` (register MCP + install bundled skill),
  3. enable Deep mode and point at a local Ghidra + JDK when found,
  4. copy the bundled skill into every known host skill dir (Qoder, Claude Code, OpenCode,
     Codex, Cursor), even hosts not yet installed,
  5. print `doctor` so you can see what connected.

## Insert into your AI

1. Run the installer for your OS (see above), or do the manual steps in `SKILL.md`.
2. Restart / reconnect the host so its MCP tool list refreshes.
3. In the host, point at a target and ask to study or recreate it. The skill drives
   `core_target_open` / `rebuild` with evidence, Deep mode and Ghidra.

## Engines

- **Ghidra**: used by Craft Anything in Deep mode (`engines.ghidra.install_dir`). Built-in
  engines still work without it; Ghidra adds richer decompilation.
- **IDA Pro**: not driven by Craft Anything. For IDA, use the companion
  `reverse-engineer-anything` (REA) MCP with `provider_id: "ida"` and treat it as separate
  evidence.

## License / brand

Upstream code remains MIT OR Apache-2.0 with its LICENSE/NOTICE/ATTRIBUTION intact. Per the
upstream brand licence, forks must remove the ArtCraft name/wordmark/logos; this fork therefore
does not ship `docs/brand/` trademark assets. The wrapper itself is offered under the same
MIT/Apache-2.0 dual licence.

## Responsible use

Only examine software you own or are permitted to examine. Never bypass DRM, logins or
anti-cheat. Rebuilds recreate layout/style/behaviour from evidence; do not copy logos, photos
or text unless you own them or have permission.