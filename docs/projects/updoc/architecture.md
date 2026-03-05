---
project: updoc
synced_from: d3ab04c
synced_at: 2026-03-05
---

# updoc — Architecture

<!-- updoc:begin -->
## Directory Structure

```
updoc/
├── .claude-plugin/
│   ├── plugin.json            # Plugin metadata (name, version, author, license)
│   └── marketplace.json       # Marketplace registration info
├── commands/                  # Claude Code skill definitions (Markdown specs)
│   ├── init.md                # /updoc:init — initialize docs repository
│   ├── up.md                  # /updoc:up — scan code, generate/update docs
│   └── uplan.md               # /updoc:uplan — create mission planning docs
├── hooks/
│   ├── hooks.json             # Hook configuration (SessionStart trigger)
│   └── session-start.sh       # Displays sync status on session start
├── scripts/
│   ├── init.sh                # Project detection and metadata extraction
│   └── up.sh                  # Sync metadata: mode detection, git operations
├── templates/
│   ├── config.yaml            # updoc.config.yaml template
│   ├── project-overview.md    # Technical overview template
│   ├── project-wiki.md        # Wiki document template
│   ├── mission.md             # Mission planning template
│   └── docs-index.md          # Documentation index template
├── CLAUDE.md                  # Development guidelines
├── README.md                  # English documentation
└── README.ko.md               # Korean documentation
```

## Entry Points

| Entry Point | Trigger | Description |
|-------------|---------|-------------|
| `commands/init.md` | `/updoc:init` | Skill spec — Claude reads and follows as procedure |
| `commands/up.md` | `/updoc:up` | Skill spec — documentation generation/sync workflow |
| `commands/uplan.md` | `/updoc:uplan` | Skill spec — mission planning with user conversation |
| `hooks/session-start.sh` | SessionStart hook | Auto-runs on every Claude Code session start |

## Module Organization

**Skills layer** (`commands/*.md`): Markdown files that define step-by-step procedures for Claude Code to follow. These are not code — they are natural language specifications that Claude interprets as instructions.

**Scripts layer** (`scripts/*.sh`): Bash scripts that handle data extraction. They parse `updoc.config.yaml` via `yq`, run git operations, and output structured JSON for Claude to consume. Scripts never generate documentation directly — they provide metadata that Claude uses to write docs.

**Templates layer** (`templates/*.md`): Markdown templates with `{placeholder}` variables and `<!-- updoc:begin/end -->` marker blocks. Claude substitutes frontmatter values and writes content inside markers.

**Hooks layer** (`hooks/`): Session-start hook that reads `updoc.config.yaml` and displays a status summary (version, project count, sync state).

## Data Flow

```
/updoc:up invoked
    │
    ├─ scripts/up.sh
    │   ├─ Reads updoc.config.yaml (via yq)
    │   ├─ For each project: git pull --ff-only
    │   ├─ Determines mode (init/sync/no_change)
    │   ├─ Computes changed_files via git diff
    │   └─ Outputs JSON array to stdout
    │
    ├─ Claude parses JSON
    │   ├─ init mode: reads templates, explores codebase, creates all doc files
    │   ├─ sync mode: reads existing docs, reads changed files, updates marker blocks
    │   └─ no_change mode: skip
    │
    └─ Updates docs/index.md and updoc.config.yaml
```

## Branch Protection

`up.sh` enforces that documentation can only be generated from the default branch. If the current branch does not match `default_branch` in config, it returns a `branch_mismatch` error. This ensures only merged, reviewed code gets documented.
<!-- updoc:end -->
