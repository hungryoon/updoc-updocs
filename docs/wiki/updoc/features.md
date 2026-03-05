---
project: updoc
---

# updoc — Features

<!-- updoc:begin -->
### Automatic Documentation Generation
Run `/updoc:up` and updoc scans your project code to produce structured documentation. No manual writing needed for the initial draft — updoc reads your source files and generates overviews, architecture descriptions, configuration references, and dependency lists.

### Smart Change Detection
updoc tracks which commit was last documented. On the next run, it only examines files that changed since then, updating just the relevant documentation sections instead of regenerating everything.

### Auto Pull
Before scanning, updoc automatically runs `git pull --ff-only` on each project to fetch the latest code. If the pull fails, it continues with the current local state and warns you.

### Version Guard
updoc checks that the config file version matches the installed plugin version before running. If there's a mismatch, it stops with a clear error message — preventing documentation from being generated with incompatible logic.

### Ask-User Policy
When updoc encounters ambiguous or unverifiable information during documentation generation, it asks you directly instead of guessing. This prevents misunderstandings from silently entering your docs.

### Safe Content Separation
Documentation files have clearly marked zones: auto-generated sections (inside marker blocks) and user-written sections (outside markers). updoc never touches your manual notes, custom explanations, or added context.

### Multi-Language Support
Documentation can be generated in English or Korean, controlled by a single setting in the config file. The language applies to all generated content across all projects.

### Mission Planning
The `/updoc:uplan` command helps you plan new features by reading your existing documentation first. It produces structured mission documents with background context, current vs. target state analysis, impact scope across projects, and task breakdowns.

### Multi-Project Hub
A single docs repository can manage documentation for multiple projects. Each project is tracked independently with its own sync state, and a central index page links everything together.

### Session Status Display
Every time you start a Claude Code session in a docs repository, updoc shows a quick status: how many projects are registered, how many are synced, and when the last sync happened.

### Workspace Protection
`/updoc:init` sets up `CLAUDE.md` rules that prevent accidental modification of source repos under `repos/` and ensure all documentation work stays within `docs/`.
<!-- updoc:end -->
