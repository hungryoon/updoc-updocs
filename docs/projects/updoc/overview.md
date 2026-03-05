---
project: updoc
synced_from: d3ab04c
synced_at: 2026-03-05
---

# updoc

<!-- updoc:begin -->
updoc is a Claude Code plugin that automatically generates and maintains technical documentation by scanning project source code. It bridges the gap between code changes and documentation freshness, ensuring docs stay synchronized with the actual codebase.

**Core philosophy**: "Rotten docs always make rotten plans. updoc keeps them fresh."

updoc operates as a documentation hub — a separate repository that clones multiple project repos into `repos/` and generates structured documentation under `docs/`. It supports two document types: **project docs** (developer-facing technical references) and **wiki docs** (plain-language service descriptions for non-developers).

The plugin provides three commands:
- `/updoc:init` — Initialize a new docs repository, detect projects, and create the config
- `/updoc:up` — Scan code and generate or update documentation (init/sync/no_change modes)
- `/updoc:uplan` — Create mission documents with cross-project impact analysis and task breakdowns

updoc uses a **marker block system** (`<!-- updoc:begin/end -->`) to separate auto-generated content from user-written notes, ensuring manual additions are never overwritten during sync.
<!-- updoc:end -->

## Sections

- [Architecture](architecture.md)
- [Configuration](configuration.md)
- [Dependencies](dependencies.md)
