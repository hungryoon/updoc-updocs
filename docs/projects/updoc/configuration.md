---
project: updoc
synced_from: d3ab04c
synced_at: 2026-03-05
---

# updoc — Configuration

<!-- updoc:begin -->
## updoc.config.yaml

The main configuration file, located at the docs repository root. Created by `/updoc:init`.

```yaml
version: "0.3.0"          # Plugin version
language: en               # Documentation language: "en" or "ko"
projects:
  - name: updoc            # Project display name
    path: ./repos/updoc    # Relative path to cloned repo
    default_branch: main   # Branch to sync docs from
    last_sync_commit: abc1234   # Last documented commit hash
    last_sync_date: "2026-03-05T04:36:14Z"  # Last sync timestamp
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
| `language` | Yes | Controls output language for all generated docs. `"en"` = English, `"ko"` = Korean. Overrides conversation language. |
| `projects[].path` | Yes | Relative path to the project repo. Typically `./repos/{name}`. |
| `projects[].default_branch` | Yes | Only this branch is eligible for doc sync. |
| `projects[].last_sync_commit` | Auto | Updated by `/updoc:up` after each sync. Used to detect changes via `git diff`. |
| `docs.path` | Yes | Root directory for all generated documentation. |

## Plugin Metadata

**`.claude-plugin/plugin.json`** — Required for Claude Code plugin registration:

```json
{
  "name": "updoc",
  "version": "0.3.0",
  "description": "Rotten docs ALWAYS make rotten plans. updoc keeps them fresh.",
  "author": { "name": "hungryoon" },
  "repository": "https://github.com/hungryoon/updoc",
  "license": "Apache-2.0"
}
```

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
| `yq` | 4+ | YAML parsing in shell scripts |
| `git` | Any | Repository operations (diff, log, pull) |
<!-- updoc:end -->
