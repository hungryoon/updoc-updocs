---
slug: version-guard-fullscan-askuser-lean-frontmatter
created: 2026-03-05
---

# version-guard-fullscan-askuser-lean-frontmatter

## Background

Four improvements identified through real-world usage of updoc v0.3.x:

1. **Version guard**: When `updoc.config.yaml` version differs from the installed plugin version, documentation may be generated with incompatible templates or logic. There is no warning — the mismatch silently produces incorrect output.
2. **Rename init → full_scan**: The `mode: "init"` output from `up.sh` conflicts with the `/updoc:init` command name, causing confusion. "init" implies first-time setup, but the mode actually means "full documentation scan from scratch."
3. **Ask user instead of guessing**: The current policy marks uncertain content as `TODO: manual verification needed`. This allows misunderstandings to propagate into documentation unnoticed. Better to ask the user directly during generation.
4. **Lean frontmatter**: All 8 doc files per project carry `synced_from` and `synced_at` in their frontmatter, but only `overview.md` is read by `up.sh` for sync state. The other 7 files have redundant frontmatter that must be kept in sync for no functional reason.

## AS-IS

- `up.sh` does not read `plugin.json` version. `updoc.config.yaml` `version` field is never validated against the running plugin version. A user can upgrade the plugin but keep an old config, or vice versa, with no warning.
- `up.sh` outputs `"mode": "init"` when no prior documentation exists. The `up.md` skill spec calls this "Init mode." The term collides with `/updoc:init`.
- `up.md` instructs Claude to mark uncertain content with `TODO: manual verification needed` and move on. The user never gets asked. Misunderstandings silently enter docs.
- All 8 generated files (overview, architecture, configuration, dependencies, index, features, access, policies) contain `synced_from` and `synced_at` in frontmatter. Only `overview.md` is read by `up.sh` (line 81-86). The other 7 files' frontmatter is write-only overhead.

## TO-BE

- `up.sh` reads `plugin.json` version (from `UPDOC_ROOT`) and compares it to `updoc.config.yaml` `version`. If they differ, output an error JSON `{"error": "version_mismatch", "config_version": "...", "plugin_version": "..."}` and exit non-zero. The `up.md` skill spec displays a clear warning message and stops.
- `up.sh` outputs `"mode": "full_scan"` instead of `"mode": "init"`. The `up.md` skill spec renames "Init mode" to "Full scan mode" throughout. No functional change — just terminology.
- `up.md` replaces the "no guessing" TODO policy with an "ask the user" policy. When Claude encounters ambiguous or unverifiable information during documentation generation, it asks the user via `AskUserQuestion` before writing. This prevents misunderstandings from entering docs.
- Only `overview.md` retains `synced_from` and `synced_at` in frontmatter. The other 7 files use a minimal frontmatter with `project` field only. Templates are updated accordingly.

## Impact Scope

### updoc

| Area | Affected File | Change |
|------|---------------|--------|
| Script | `scripts/up.sh` | Add version comparison logic; change `"init"` → `"full_scan"` in mode output |
| Skill spec | `commands/up.md` | Version mismatch error handling; rename "Init mode" → "Full scan mode"; replace TODO policy with ask-user policy; update frontmatter instructions for non-overview files |
| Template | `templates/project-overview.md` | No change (already has synced_from/synced_at) |
| Template | `templates/project-architecture.md` | Remove synced_from, synced_at from frontmatter |
| Template | `templates/project-configuration.md` | Remove synced_from, synced_at from frontmatter |
| Template | `templates/project-dependencies.md` | Remove synced_from, synced_at from frontmatter |
| Template | `templates/wiki-index.md` | Remove synced_from, synced_at from frontmatter |

## API Contracts

N/A (single-project, no cross-project communication)

## Tasks

### updoc

**Version guard**
- updoc-1: Add version comparison to `up.sh`. Read plugin version from `$UPDOC_ROOT/.claude-plugin/plugin.json` (via `yq` or `jq`). Compare with `updoc.config.yaml` `version` field. On mismatch, output `{"error": "version_mismatch", "config_version": "X", "plugin_version": "Y"}` and exit 1. The script needs `UPDOC_ROOT` passed as an environment variable or argument.
- updoc-2: Add version mismatch error handling to `up.md` Step 3. Display: "Config version ({config_version}) does not match plugin version ({plugin_version}). Please update updoc.config.yaml or reinstall the plugin."

**Rename init → full_scan**
- updoc-3: Change `up.sh` line 117 from `mode="init"` to `mode="full_scan"`
- updoc-4: Rename all references in `up.md` from "Init mode" / `mode == "init"` to "Full scan mode" / `mode == "full_scan"`

**Ask user instead of guessing**
- updoc-5: Replace the "No guessing" instruction in `up.md` Step 4 content guidelines. Current: "If uncertain about something, mark `TODO: manual verification needed`". New: "If uncertain about something, ask the user using AskUserQuestion before writing. Do not guess or leave TODOs — resolve ambiguity before documenting."

**Lean frontmatter**
- updoc-6: Update templates `project-architecture.md`, `project-configuration.md`, `project-dependencies.md`, `wiki-index.md` — remove `synced_from` and `synced_at` lines from frontmatter, keep only `project: {name}`
- updoc-7: Update `up.md` Init/Full scan mode instructions: only `overview.md` gets `synced_from`/`synced_at` in frontmatter. Other files get `project: {name}` only.
- updoc-8: Update `up.md` Sync mode instructions: only update `synced_from`/`synced_at` in `overview.md` frontmatter. Other files: replace marker block content only, no frontmatter update.
