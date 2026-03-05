---
project: updoc
synced_from: d3ab04c
synced_at: 2026-03-05
---

# updoc — Dependencies

<!-- updoc:begin -->
updoc has no package manager dependencies (no package.json, go.mod, pyproject.toml, etc.). It is a pure shell + Markdown plugin that runs entirely within Claude Code.

## Runtime Dependencies

| Dependency | Type | Purpose |
|------------|------|---------|
| Claude Code | Host | Plugin runtime — executes skills, provides tool access (Read, Write, Glob, Grep, etc.) |
| bash 3.2+ | System | Executes `scripts/init.sh`, `scripts/up.sh`, `hooks/session-start.sh` |
| yq 4+ | System | Parses `updoc.config.yaml` in shell scripts |
| git | System | Repository operations: `git diff`, `git log`, `git pull --ff-only`, branch detection |

## Plugin System Dependencies

updoc relies on the Claude Code plugin system:
- **Skills**: `commands/*.md` files are registered as `/updoc:*` slash commands
- **Hooks**: `hooks/hooks.json` registers SessionStart events
- **Plugin discovery**: `.claude-plugin/plugin.json` provides metadata for installation and marketplace listing

## No External APIs

updoc does not call any external APIs or services. All operations are local: reading files, running git commands, and writing documentation.
<!-- updoc:end -->
