---
project: updoc
type: claude-code-plugin
synced_from: d3ab04c
synced_at: 2026-03-05
---

# updoc

<!-- updoc:begin -->

## Overview

A Claude Code plugin that scans project codebases to generate and maintain living documentation, then uses that documentation to plan features with cross-project awareness. Built entirely with shell scripts and markdown skill specs — no build step, no runtime dependencies beyond `bash 3.2+`, `yq 4+`, and `git`.

Three commands form the core workflow: `/updoc:init` sets up the workspace, `/updoc:up` generates or updates technical docs from merged code, and `/updoc:uplan` creates mission (planning) documents based on those docs.

## Directory Structure

```
.
├── .claude-plugin/        # Plugin metadata (plugin.json, marketplace.json)
├── commands/              # Skill specs invoked as slash commands
│   ├── init.md            # /updoc:init — workspace initialization
│   ├── up.md              # /updoc:up — documentation generation/sync
│   └── uplan.md           # /updoc:uplan — mission planning
├── hooks/                 # Session lifecycle hooks
│   ├── hooks.json         # Hook registration (SessionStart)
│   └── session-start.sh   # Displays project sync status on session start
├── scripts/               # Shell scripts for orchestration
│   ├── init.sh            # Project detection under repos/
│   ├── up.sh              # Scan orchestration — validates projects, outputs JSON
│   └── update-sync-state.sh  # Updates sync commit/date in config
├── templates/             # Config and document templates
│   ├── config.yaml        # Default config structure (YAML)
│   ├── docs-index.md      # Hub index template
│   ├── mission.md         # Mission document template
│   ├── project-overview.md  # Project overview template
│   └── project-wiki.md    # Wiki document template
├── CLAUDE.md              # Development instructions for Claude
├── README.md              # English documentation
├── README.ko.md           # Korean documentation
└── updoc.config.yaml      # Workspace config (committed)
```

## Entry Points

Shell-script-based Claude Code plugin with no traditional entry points. Invoked via:

- `/updoc:init` → loads `commands/init.md` skill spec → runs `scripts/init.sh` for project detection → creates config and directory structure
- `/updoc:up` → loads `commands/up.md` skill spec → runs `scripts/up.sh` for metadata → Claude explores code and writes docs
- `/updoc:uplan` → loads `commands/uplan.md` skill spec → reads existing docs → Claude creates mission plans
- Session start → `hooks/session-start.sh` displays sync status automatically

## Configuration Files

- `updoc.config.yaml` — per-workspace config: language, project list (`name`, `path`, `default_branch`), docs output paths (`path`, `projects_dir`, `wiki_dir`, `missions_dir`)
- `.claude-plugin/plugin.json` — Claude Code plugin metadata (name, version, description, commands path)
- `.claude-plugin/marketplace.json` — marketplace registration metadata
- `templates/config.yaml` — default config template for new installations

## Dependencies

No package-managed dependencies. Runtime requirements:
- `bash` 3.2+
- `yq` 4+
- `git`

<!-- updoc:end -->
