---
name: craft-anything-anywhere
description: Insert Craft Anything into any AI host (Qoder, Claude Code, OpenCode, Codex, Cursor, ...). Bootstraps the original unmodified craft-anything CLI/MCP, enables Deep mode with Ghidra (and IDA via REA when present), then reverse-engineers a target and recreates it. Use when the user wants to study or remake a shipped app, website or game from inside their own AI.
metadata:
  version: "1"
  upstream: https://github.com/storytold/craft-anything
---

# Craft Anything, anywhere

This is a thin, host-agnostic wrapper around the **original, unmodified** Craft Anything
engine (MIT/Apache-2.0). It adds no engine code; it only installs, connects and orchestrates
the upstream CLI + MCP server so any agent can drive it. Upstream behaviour, tools and
evidence contract are authoritative; trust the live tool list over this file.

## 1. Bootstrap (once per machine)

If `craft-anything-cli` is not on PATH, run the installer for your OS from this repo:

- Windows: `powershell -ExecutionPolicy Bypass -File anywhere/install.ps1`
- macOS/Linux: `bash anywhere/install.sh`

The installer downloads the official release CLI, runs `craft-anything-cli setup
--all-detected --yes`, turns on Deep mode, points Ghidra at a local install when found,
and copies the bundled skill into every known host skill directory. It never edits
upstream source.

Manual equivalent:

```sh
craft-anything-cli setup --all-detected --yes
craft-anything-cli settings_set --key deep_mode --value true
craft-anything-cli settings_set --key engines.ghidra.install_dir --value '"<ghidra dir>"'
```

After setup, restart/reconnect the host so its MCP tool list refreshes.

## 2. Connect check

- If `craft-anything` (or `craft_anything`) MCP tools are in your tool list, go straight to work.
- If absent: `craft-anything-cli doctor`, then `setup --dry-run`, show the plan, apply, restart.
- Only a core toolset is listed first: `list_toolsets` then `enable_toolset` (native, web, Android...).
- Deep mode off => Clean room: inside-the-target tools are refused with reason `clean_room`.
  Enable Deep (`settings_set deep_mode true` or server `--deep`); do not work around it.

## 3. Engines: Ghidra and IDA

- **Ghidra** (Deep mode): set `engines.ghidra.install_dir` to a local Ghidra (e.g.
  `.../ghidra_12.1.4_PUBLIC`) and `engines.ghidra.java_home` to a JDK. Verify with
  `craft-anything-cli doctor` (`engine:ghidra => ok`). Craft Anything's built-in engines
  still work without Ghidra; Ghidra only adds richer decompilation.
- **IDA Pro**: Craft Anything does not drive IDA. When the user wants IDA, use the
  companion `reverse-engineer-anything` (REA) MCP with `provider_id: "ida"` on the same
  target, and treat its output as separate evidence. Do not claim Craft Anything used IDA.

## 4. Study -> recreate workflow

1. Resolve the actual target (path or URL). If the user says "my app" without naming it, ask.
2. Keep working memory: `core_investigation_show`, `core_brief_write`, `core_plan_update`;
   record hypotheses/findings/handoff as upstream's skill describes.
3. Open the target: `core_target_open`; enable the matching toolset; inspect with evidence ids.
4. To recreate: `craft-anything-cli rebuild <target-or--> "<what to make>"` (or the MCP
   rebuild workflow). It studies, builds with the host's coding agent, checks against the
   original and refines. State the stack you want in the request.
5. Cite evidence ids; label observed vs derived vs inferred; record unknowns; write handoff.

## 5. Consent and safety

- Tools that run target code or change a device need explicit user OK (`consent_required`).
- Only examine software you own or are permitted to examine; never bypass DRM/logins/anti-cheat.
- Rebuilds recreate layout/style/behaviour from evidence; do not copy logos/photos/text
  unless the user confirms ownership or permission.
- Never put secrets or personal data into notes, findings or handoffs.

## 6. Host install matrix

| Host | Skill dir | MCP registration |
|---|---|---|
| Qoder | `~/.qoder/skills/craft-anything` | `~/.qoder/settings.json` mcpServers (setup) |
| Claude Code | `~/.claude/skills/craft-anything` | `~/.claude.json` (setup) |
| OpenCode | `~/.agents/skills/craft-anything` | `~/.config/opencode/opencode.json` (setup) |
| Codex | `~/.codex/skills/craft-anything` | `~/.codex/config.toml` (setup) |
| Cursor | `~/.cursor/skills/craft-anything` | `~/.cursor/mcp.json` (setup) |

`setup --client <id>` handles detected hosts; the installer also copies the skill into
undetected hosts' dirs so it is ready when they are installed.