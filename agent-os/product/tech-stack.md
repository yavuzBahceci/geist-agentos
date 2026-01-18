# Geist - Tech Stack

## Overview

Geist is a shell-based developer tool that uses markdown templates and YAML configuration. It has no runtime dependencies beyond bash and standard Unix utilities.

---

## Languages

| Language | Usage | Percentage |
|----------|-------|------------|
| **Shell (Bash)** | Installation scripts, automation | 5% |
| **Markdown** | Command templates, workflows, documentation | 90% |
| **YAML** | Configuration files | 5% |

### Primary: Shell (Bash)
- Installation and update scripts
- Template compilation
- File operations

### Secondary: Markdown
- Command templates with embedded logic
- Workflow definitions
- Standards and documentation
- AI prompt templates

### Configuration: YAML
- `config.yml` - Global configuration
- `workflow-config.yml` - Workflow settings
- `project-profile.yml` - Per-project settings

---

## Project Type

| Characteristic | Value |
|----------------|-------|
| **Type** | Developer Tool / CLI |
| **Architecture** | Template-based |
| **UI** | None (CLI only) |
| **API** | None |
| **Database** | None |

---

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                        GEIST ARCHITECTURE                    │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│  PROFILES (Source Templates)                                 │
│  profiles/default/                                           │
│  ├── commands/      → Command templates                      │
│  ├── workflows/     → Reusable workflow templates            │
│  ├── standards/     → Coding standards                       │
│  ├── agents/        → Agent definitions                      │
│  └── docs/          → Documentation                          │
└─────────────────────────────────────────────────────────────┘
                              │
                              │ Installation (scripts/)
                              ▼
┌─────────────────────────────────────────────────────────────┐
│  AGENT-OS (Installed in Target Project)                      │
│  your-project/agent-os/                                      │
│  ├── commands/      → Compiled commands                      │
│  ├── workflows/     → Compiled workflows                     │
│  ├── standards/     → Compiled standards                     │
│  ├── agents/        → Compiled agents                        │
│  ├── product/       → Product documentation                  │
│  ├── basepoints/    → Codebase documentation                 │
│  ├── config/        → Project configuration                  │
│  └── output/        → Generated outputs                      │
└─────────────────────────────────────────────────────────────┘
```

---

## Key Components

### 1. Scripts (`scripts/`)
| Script | Purpose |
|--------|---------|
| `project-install.sh` | Install agent-os into a project |
| `project-update.sh` | Update existing installation |
| `common-functions.sh` | Shared shell functions |
| `create-profile.sh` | Create new profile |

### 2. Commands (`profiles/default/commands/`)
| Command | Purpose |
|---------|---------|
| `adapt-to-product` | Extract product info from codebase |
| `plan-product` | Plan new product |
| `create-basepoints` | Document codebase patterns |
| `deploy-agents` | Specialize commands |
| `shape-spec` | Gather requirements |
| `write-spec` | Write specification |
| `create-tasks` | Break into tasks |
| `implement-tasks` | Implement code |
| `orchestrate-tasks` | Multi-agent orchestration |

### 3. Workflows (`profiles/default/workflows/`)
| Category | Purpose |
|----------|---------|
| `detection/` | Project detection workflows |
| `basepoints/` | Basepoint extraction |
| `planning/` | Product planning |
| `specification/` | Spec writing |
| `implementation/` | Code implementation |
| `validation/` | Validation checks |
| `cleanup/` | Cleanup workflows |

### 4. Standards (`profiles/default/standards/`)
| Category | Purpose |
|----------|---------|
| `global/` | Universal coding standards |
| `documentation/` | Documentation standards |
| `process/` | Development workflow |
| `quality/` | Quality assurance |
| `testing/` | Test writing |

---

## Dependencies

### Runtime Dependencies
- **Bash** (v4.0+) - Script execution
- **Standard Unix utilities** - `sed`, `awk`, `grep`, `find`, `mkdir`, `cp`
- **Git** - Version control (optional)

### No External Dependencies
- No package manager required
- No build step
- No compilation
- Pure shell + markdown

---

## Build & Validation

| Command | Script |
|---------|--------|
| **Install** | `scripts/project-install.sh --profile default` |
| **Update** | `scripts/project-update.sh` |
| **Test** | None (manual validation) |
| **Lint** | None |

---

## File Conventions

### Template Syntax
```markdown
{{IF condition}}
  Content if true
{{ENDIF}}

{{workflows/path/to/workflow}}

{{standards/category/*}}

{{PHASE N: @agent-os/commands/path/file.md}}
```

### Placeholder Variables
```
{{PROJECT_BUILD_COMMAND}}
{{PROJECT_TEST_COMMAND}}
{{PROJECT_LINT_COMMAND}}
{{BASEPOINTS_PATH}}
```

---

## Compatibility

| Platform | Status |
|----------|--------|
| macOS | ✅ Supported |
| Linux | ✅ Supported |
| Windows (WSL) | ✅ Supported |
| Windows (native) | ❌ Not supported |

| Shell | Status |
|-------|--------|
| Bash 4.0+ | ✅ Supported |
| Zsh | ✅ Supported |
| Fish | ⚠️ Untested |
| PowerShell | ❌ Not supported |
