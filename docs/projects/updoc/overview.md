---
project: updoc
synced_from: 5e75c5a
synced_at: 2026-03-05
---

# updoc

<!-- updoc:begin -->
updoc is a Claude Code plugin that automatically generates and maintains technical documentation by scanning project source code. It bridges the gap between code changes and documentation freshness, ensuring docs stay synchronized with the actual codebase.

**Core philosophy**: "Rotten docs always make rotten plans. updoc keeps them fresh."

updoc operates as a documentation hub — a separate repository that clones multiple project repos into `repos/` and generates structured documentation under `docs/`. It supports two document types: **project docs** (developer-facing technical references) and **wiki docs** (plain-language service descriptions for non-developers).

The plugin provides three commands:
- `/updoc:init` — Initialize a new docs repository, detect projects, create config and workspace rules
- `/updoc:up` — Scan code and generate or update documentation (full_scan/sync/no_change modes)
- `/updoc:uplan` — Create mission documents with cross-project impact analysis and task breakdowns

Key design principles:
- **Marker block system** (`<!-- updoc:begin/end -->`) separates auto-generated content from user-written notes
- **Version guard** prevents running with mismatched config and plugin versions
- **Ask-user policy** resolves ambiguous content by asking the user instead of guessing
- **Auto pull** fetches latest code before scanning to ensure docs reflect the newest state
<!-- updoc:end -->

## Sections

- [Architecture](architecture.md)
- [Configuration](configuration.md)
- [Dependencies](dependencies.md)
