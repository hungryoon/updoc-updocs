---
slug: auto-pull-modular-docs-repos-guard
created: 2026-03-05
---

# auto-pull-modular-docs-repos-guard

## Background

Four improvements discovered through real-world use of the updoc plugin:

1. **Auto pull**: Projects under `repos/` may not have the latest code, causing documentation to sync against stale commits
2. **Modular docs**: Single large files per project (overview.md, wiki.md) cause merge conflicts during concurrent work. Documentation should be split into section files, with directories and hub pages linking them together
3. **Repos guard**: Files under `repos/` in an updoc-generated repository can be accidentally modified — they should be read-only. A CLAUDE.md rule should enforce this at init time
4. **Language enforcement**: Document output language follows conversation language (e.g. CLAUDE.local.md) instead of the `language` field in updoc.config.yaml

## AS-IS

- `up.sh` calculates diffs against local HEAD without running git pull. New remote commits are not reflected
- One `overview.md` per project (all sections in a single file) + one `wiki/{name}.md`. Each marker block is 50-120 lines — high conflict risk
- `/updoc:init` does not create CLAUDE.md with repos/ protection rules
- Language instructions in `up.md` and `uplan.md` are weak ("Write in the language setting") and get overridden by conversation-level language settings like CLAUDE.local.md

## TO-BE

- `up.sh` runs `git pull --ff-only` after branch validation, before diff calculation. On failure: stderr warning only, continue with local HEAD
- Section-level file splitting:
  ```
  docs/projects/{name}/
  ├── overview.md        # Project summary + section links (hub)
  ├── architecture.md    # Directory structure, entry points
  ├── configuration.md   # Config files, environment variables
  └── dependencies.md    # Dependencies, runtime requirements

  docs/wiki/{name}/
  ├── index.md           # Service summary + section links (hub)
  ├── features.md        # Key features
  ├── access.md          # Access methods, URLs, environments
  └── policies.md        # Business rules, settings
  ```
- `/updoc:init` automatically adds repos/ modification ban to CLAUDE.md
- Explicit strong instruction in `up.md` and `uplan.md` to enforce config `language` setting, overriding conversation language

## Impact Scope

### updoc

| Area | Affected File | Change |
|------|---------------|--------|
| Script | `scripts/up.sh` | Add git pull logic + `pull_status` field to JSON output |
| Skill spec | `commands/up.md` | Pull documentation + modular docs rewrite + language enforcement |
| Skill spec | `commands/uplan.md` | Language enforcement instruction |
| Skill spec | `commands/init.md` | CLAUDE.md creation step + wiki link directory format |
| Template | `templates/project-overview.md` | Change to hub role (summary + section links) |
| Template | `templates/project-architecture.md` | New |
| Template | `templates/project-configuration.md` | New |
| Template | `templates/project-dependencies.md` | New |
| Template | `templates/wiki-index.md` | New |
| Template | `templates/project-wiki.md` | Delete |

## API Contracts

N/A (single-project mission)

## Tasks

### updoc

**Auto pull**
- updoc-1: Add `git pull --ff-only` logic to `up.sh` (after branch validation at line ~110, before Mode & Diff at line ~112). Add `pull_status` field to JSON output: `"pulled"` / `"up_to_date"` / `"pull_failed"`
- updoc-2: Document pull behavior in `up.md` Step 2 description. Add pull status to Step 5 change report

**Modular docs — Templates**
- updoc-3: Create 4 new section templates in `templates/`: `project-architecture.md` (section: architecture), `project-configuration.md` (section: configuration), `project-dependencies.md` (section: dependencies), `wiki-index.md` (section: wiki-index). Each with frontmatter `{name}`, `{commit}`, `{date}` and `<!-- updoc:begin/end -->` markers
- updoc-4: Modify `templates/project-overview.md` to hub role — add `## Sections` with links to architecture.md, configuration.md, dependencies.md
- updoc-10: Delete `templates/project-wiki.md`

**Modular docs — Skill spec**
- updoc-5: Rewrite `up.md` Init mode for modular file generation:
  - Directories: `{projects_dir}/{name}/` + `{wiki_dir}/{name}/` + `{missions_dir}/`
  - Overview files (4): overview.md (hub from template), architecture.md, configuration.md, dependencies.md (each from template)
  - Wiki files (4): index.md (hub from wiki-index template), features.md, access.md, policies.md (structure defined inline in up.md, no template)
  - Overview content: overview.md gets project description; architecture.md gets directory structure + entry points; configuration.md gets config files + env vars; dependencies.md gets dependencies
  - Wiki content: index.md gets "what is this" summary; features.md gets key features; access.md gets URLs/environments; policies.md gets business rules/settings
- updoc-6: Rewrite `up.md` Sync mode for per-section updates:
  - Replace marker block independently in each section file
  - Analyze `changed_files` to determine which sections need updating (not all)
  - Update `synced_from`/`synced_at` frontmatter in each updated file
- updoc-7: Update `up.md` Step 4.5 + Step 5:
  - Wiki link format: `wiki/{name}.md` → `wiki/{name}/index.md`
  - Change report: list each file individually

**Language enforcement**
- updoc-11: Add strong language override instruction to `up.md` (Step 4 top) and `uplan.md` (Step 6 top):
  - Current: "Write in the language setting (en = English, ko = Korean)" — weak, gets overridden
  - New: "**IMPORTANT: All generated document content MUST be written in the language specified by the `language` field in the JSON output (not the conversation language). If language is `en`, write in English. If `ko`, write in Korean. This overrides any other language instructions.**"

**Repos guard**
- updoc-8: Add "Step 4.5: Create CLAUDE.md" to `init.md` after Step 4:
  - If CLAUDE.md doesn't exist, create it with updoc Rules section
  - If CLAUDE.md exists, append updoc Rules section (if not already present)
  - Content: repos/ modification ban + docs/-only work rule
- updoc-9: Update `init.md` Step 9 wiki link from `wiki/{name}.md` to `wiki/{name}/index.md`
