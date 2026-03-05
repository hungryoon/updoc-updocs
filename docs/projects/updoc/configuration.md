---
project: updoc
---

# updoc — Configuration

<!-- updoc:begin -->
## updoc.config.yaml

The main configuration file, located at the docs repository root. Created by `/updoc:init`.

```yaml
version: "0.3.2"          # Must match plugin version
language: en               # Documentation language: "en" or "ko"
projects:
  - name: updoc            # Project display name
    path: ./repos/updoc    # Relative path to cloned repo
    default_branch: main   # Branch to sync docs from
    type: claude-code-plugin    # Optional project type hint
docs:
  path: ./docs             # Documentation output directory
  projects_dir: projects   # Technical docs subdirectory
  wiki_dir: wiki           # Wiki docs subdirectory
  missions_dir: missions   # Mission docs subdirectory
```

### Key Fields

| Field | Required | Description |
|-------|----------|-------------|
| `version` | Yes | Must match the installed plugin version. Mismatch triggers a version guard error. |
| `language` | Yes | Controls output language for all generated docs. `"en"` = English, `"ko"` = Korean. Overrides conversation language. |
| `projects[].path` | Yes | Relative path to the project repo. Typically `./repos/{name}`. |
| `projects[].default_branch` | Yes | Only this branch is eligible for doc sync. |
| `docs.path` | Yes | Root directory for all generated documentation. |

### Sync Tracking

Sync state (`synced_from`, `synced_at`) is stored in the frontmatter of `overview.md` only — not in `updoc.config.yaml` or other doc files. `up.sh` reads `overview.md` frontmatter to determine the last synced commit for change detection.

## Plugin Metadata

**`.claude-plugin/plugin.json`** — Required for Claude Code plugin registration:

```json
{
  "name": "updoc",
  "version": "0.3.2",
  "description": "Rotten docs ALWAYS make rotten plans. updoc keeps them fresh.",
  "author": { "name": "hungryoon" },
  "repository": "https://github.com/hungryoon/updoc",
  "license": "Apache-2.0"
}
```

The `version` field here is compared against `updoc.config.yaml`'s `version` by the version guard in `up.sh`.

## Hook Configuration

**`hooks/hooks.json`** — Registers the session-start hook:

```json
{
  "hooks": [
    {
      "event": "SessionStart",
      "command": "bash hooks/session-start.sh"
    }
  ]
}
```

## Environment Requirements

updoc scripts require the following tools to be available in PATH:

| Tool | Version | Purpose |
|------|---------|---------|
| `bash` | 3.2+ | Script execution |
| `yq` | 4+ | YAML and JSON parsing in shell scripts |
| `git` | Any | Repository operations (diff, log, pull) |

## Environment Variables

| Variable | Used By | Description |
|----------|---------|-------------|
| `UPDOC_ROOT` | `up.sh` | Path to the plugin root directory. Passed by `up.md` skill spec. Required for version guard (reads `plugin.json` from this path). |
<!-- updoc:end -->
