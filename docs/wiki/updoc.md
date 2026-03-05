---
project: updoc
type: claude-code-plugin
synced_from: d3ab04c
synced_at: 2026-03-05
---

# updoc

<!-- updoc:begin -->

## What is this?

updoc is a documentation plugin for Claude Code. It solves a common problem: documentation that falls behind the code. When you ask Claude to plan a feature, it reads your docs — but if those docs are weeks old, the plan references renamed modules, deleted endpoints, and abandoned patterns. Tokens wasted, plans wrong.

updoc fixes this by creating a continuous loop: code changes get merged, `/updoc:up` regenerates docs from the actual codebase, and `/updoc:uplan` uses those fresh docs to plan the next feature. The cycle repeats with every merge, so documentation never falls behind.

The philosophy is simple: **rotten docs always make rotten plans**. updoc keeps them fresh.

## Key Features

### Workspace Initialization (`/updoc:init`)

- Detects projects under `repos/` directory automatically
- Creates directory structure (`docs/projects/`, `docs/wiki/`, `docs/missions/`)
- Generates `updoc.config.yaml` with detected projects and language preference
- Creates `docs/index.md` hub page linking all project documentation
- Sets up `.gitignore` to exclude `repos/` from version control
- Warns if run inside a code project (updoc works best in a dedicated docs repo)

### Documentation Generation (`/updoc:up`)

- Scans your project's source code directly (using Glob, Grep, and Read) to produce structured technical documentation
- Generates two types of docs per project:
  - **Overview** (developer audience): directory structure, entry points, config files, dependencies
  - **Wiki** (non-developer audience): what the service does, key features, how to access it
- First run creates everything from scratch; subsequent runs only update based on changed files since last sync
- Uses a **marker block system** (`<!-- updoc:begin/end -->`) — auto-generated content stays inside markers, while your hand-written notes outside the markers are never touched through any number of syncs

### Mission Planning (`/updoc:uplan`)

- Creates feature planning documents (called "missions") based on your current documentation
- Produces structured plans with:
  - **Background**: Why the feature is needed
  - **AS-IS / TO-BE**: Current state vs. target state
  - **Impact Scope**: Which projects and which parts get affected
  - **API Contracts**: Cross-project endpoint definitions with request/response schemas
  - **Task List**: Concrete tasks attributed to each project (e.g., `api-1`, `web-2`)
- Plans are based on docs only — if docs don't exist for a project, updoc tells you to run `/updoc:up` first instead of guessing

### Multi-Project Support

- Manages documentation across multiple projects simultaneously (monorepo or multi-repo)
- Hub mode: a dedicated docs repo with project repos cloned into `repos/`
- Single command documents all registered projects at once
- Cross-project impact analysis and API contract generation during planning

### Safety Guarantees

- **Branch protection**: `/updoc:up` only runs on the default branch (main/master). Feature branches are blocked, ensuring docs reflect merged, production-ready code only
- **No guessing**: If something can't be confirmed from the codebase, it's marked `TODO: manual verification needed` instead of being fabricated
- **Sync loop prevention**: updoc's own output files are excluded from change detection, so running `/updoc:up` after committing docs won't trigger unnecessary re-sync

## How to Access

### Installation

```
/plugin marketplace add hungryoon/updoc
/plugin install updoc@updoc
```

### Commands

| Command | Purpose | When to Use |
|---------|---------|-------------|
| `/updoc:init` | Initialize updoc in current directory | First time setup |
| `/updoc:up` | Generate or update documentation | After merging code changes |
| `/updoc:uplan "feature"` | Create a mission plan | When planning a new feature |

### Session Start

When you start a Claude Code session in a directory with updoc configured, it automatically shows sync status:

```
📋 updoc v0.3.0
Projects: 3 registered / 3 synced
Last sync: 2026-03-03 (a1b2c3d)

💡 /updoc:up — Update docs  |  /updoc:uplan — Start planning
```

### Typical Workflow

1. Create a dedicated docs repo and run `/updoc:init`
2. Clone project repos into `repos/`
3. Run `/updoc:up` to generate initial documentation
4. Develop features on branches, merge to main
5. Run `/updoc:up` again — docs update incrementally
6. Run `/updoc:uplan "feature name"` to plan the next feature using fresh docs
7. Repeat from step 4

## Policies & Configuration

### Language

- Configurable output language: `en` (English) or `ko` (Korean)
- Controls both CLI output messages and generated documentation content
- Set during `/updoc:init` or in `updoc.config.yaml`

### Documentation Output

- **Docs path**: configurable root directory (default: `./docs`)
- **Project docs**: `docs/projects/{name}/overview.md` — developer-focused technical docs
- **Wiki docs**: `docs/wiki/{name}.md` — non-developer service descriptions
- **Mission docs**: `docs/missions/{date}-{slug}.md` — feature planning documents
- **Index**: `docs/index.md` — hub-level table linking all project docs

### Sync State

- Each project tracks its last synced git commit hash and date in the overview.md frontmatter (`synced_from`, `synced_at`)
- Enables incremental updates: only files changed since last sync are re-analyzed
- When no code changes are detected, the project is skipped entirely

### What updoc Does Not Do

- Does not generate code — it documents and plans only
- Does not implement features — pairs with tools like Superpowers for execution
- Does not have opinions about your tech stack — works with any language or framework
- Does not require any runtime dependencies beyond `bash`, `yq`, and `git`

<!-- updoc:end -->
