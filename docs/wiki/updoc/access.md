---
project: updoc
---

# updoc — Access

<!-- updoc:begin -->
### Installation
updoc is installed as a Claude Code plugin. It can be installed from the plugin marketplace or directly from the GitHub repository: https://github.com/hungryoon/updoc

### Usage
updoc runs inside Claude Code terminal sessions. Available commands:

| Command | Purpose |
|---------|---------|
| `/updoc:init` | Set up a new docs repository |
| `/updoc:up` | Generate or update documentation |
| `/updoc:uplan "title"` | Create a mission planning document |

### Repository Structure
The docs repository follows this layout:
- `repos/` — Cloned project repositories (gitignored)
- `docs/` — Generated documentation output
- `updoc.config.yaml` — Project registry and settings

### Environments
updoc operates entirely locally. There are no staging or production environments — documentation is generated on the developer's machine and committed to the docs repository.
<!-- updoc:end -->
