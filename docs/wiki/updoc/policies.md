---
project: updoc
---

# updoc — Policies

<!-- updoc:begin -->
### Branch Protection
Documentation can only be generated from the default branch (typically `main`). Running `/updoc:up` on a feature branch will produce an error. This ensures only merged, reviewed code gets documented.

### Version Compatibility
The `version` field in `updoc.config.yaml` must match the installed plugin version. If they differ, `/updoc:up` will refuse to run and display an error. Update your config version or reinstall the plugin to resolve.

### Content Ownership
- **Inside marker blocks** (`<!-- updoc:begin/end -->`): Owned by updoc. Regenerated on every sync.
- **Outside marker blocks**: Owned by the user. Never modified by updoc.

### Language Policy
The `language` field in `updoc.config.yaml` determines the language for all generated documentation. This setting overrides the conversation language — if config says `"en"`, docs are written in English regardless of the user's chat language.

### Ask-User Policy
updoc only documents what it can verify from source code. If something cannot be confirmed by reading the codebase, updoc asks the user directly rather than guessing or leaving TODOs. This ensures no misunderstandings enter the documentation.

### Sync Tracking
Sync state (`synced_from` commit hash, `synced_at` date) is tracked in the frontmatter of `overview.md` only. Other documentation files carry minimal frontmatter. This creates a single source of truth for when documentation was last refreshed.

### Workspace Protection
`/updoc:init` creates `CLAUDE.md` rules that enforce:
- Files under `repos/` are read-only (source clones managed by git)
- All documentation work happens in `docs/`
- Marker block boundaries are respected
<!-- updoc:end -->
