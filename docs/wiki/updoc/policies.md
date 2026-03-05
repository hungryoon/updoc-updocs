---
project: updoc
synced_from: d3ab04c
synced_at: 2026-03-05
---

# updoc — Policies

<!-- updoc:begin -->
### Branch Protection
Documentation can only be generated from the default branch (typically `main`). Running `/updoc:up` on a feature branch will produce an error. This ensures only merged, reviewed code gets documented.

### Content Ownership
- **Inside marker blocks** (`<!-- updoc:begin/end -->`): Owned by updoc. Regenerated on every sync.
- **Outside marker blocks**: Owned by the user. Never modified by updoc.

### Language Policy
The `language` field in `updoc.config.yaml` determines the language for all generated documentation. This setting overrides the conversation language — if config says `"en"`, docs are written in English regardless of the user's chat language.

### No Guessing Policy
updoc only documents what it can verify from source code. If something cannot be confirmed by reading the codebase, it is marked with `TODO: manual verification needed` rather than assumed.

### Sync Tracking
Each project's `last_sync_commit` and `last_sync_date` are updated in `updoc.config.yaml` after every successful documentation run. This creates an auditable record of when documentation was last refreshed.
<!-- updoc:end -->
