# Detected Abstraction Layers

## Project: Geist

### Layer Analysis

Based on the directory structure, Geist has the following abstraction layers:

```
┌─────────────────────────────────────────────────────────────┐
│                    GEIST LAYER STRUCTURE                     │
└─────────────────────────────────────────────────────────────┘

Level 0 (Root)
├── scripts/              → Installation & Automation Layer
└── profiles/             → Template Source Layer
    └── default/          → Default Profile
        ├── commands/     → Command Templates Layer
        ├── workflows/    → Workflow Templates Layer
        ├── standards/    → Standards Layer
        ├── agents/       → Agent Definitions Layer
        └── docs/         → Documentation Layer
```

### Detected Layers

| Layer | Path | Purpose | File Count |
|-------|------|---------|------------|
| **Root** | `/` | Project root, config, docs | 5 |
| **Scripts** | `scripts/` | Installation automation | 4 |
| **Profiles** | `profiles/default/` | Template source | ~110 |
| **Commands** | `profiles/default/commands/` | Command templates | ~50 |
| **Workflows** | `profiles/default/workflows/` | Reusable workflows | ~80 |
| **Standards** | `profiles/default/standards/` | Coding standards | ~14 |
| **Agents** | `profiles/default/agents/` | Agent definitions | ~13 |
| **Docs** | `profiles/default/docs/` | Documentation | ~15 |

### Layer Relationships

```
                         ┌─────────────┐
                         │   scripts/  │
                         │ (installer) │
                         └──────┬──────┘
                                │ installs
                                ▼
┌───────────────────────────────────────────────────────────────┐
│                    profiles/default/                           │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐            │
│  │  commands/  │◄─┤  workflows/ │  │  standards/ │            │
│  │             │  │             │  │             │            │
│  │ (use)       │  │ (reusable)  │  │ (reference) │            │
│  └─────────────┘  └─────────────┘  └─────────────┘            │
│         │                │                │                    │
│         └────────────────┼────────────────┘                    │
│                          │                                     │
│                    ┌─────┴─────┐                               │
│                    │  agents/  │                               │
│                    │ (execute) │                               │
│                    └───────────┘                               │
└───────────────────────────────────────────────────────────────┘
```

### Folders Requiring Basepoints

Based on the hierarchical rule (folders with code OR multiple code-containing subfolders):

**Level 1 (Top-level):**
- `scripts/` - Contains shell scripts
- `profiles/` - Contains default profile

**Level 2 (Profile level):**
- `profiles/default/` - Contains all template categories

**Level 3 (Category level):**
- `profiles/default/commands/` - Contains command folders
- `profiles/default/workflows/` - Contains workflow categories
- `profiles/default/standards/` - Contains standard categories
- `profiles/default/agents/` - Contains agent definitions
- `profiles/default/docs/` - Contains documentation

**Level 4 (Subcategory level):**
- `profiles/default/commands/[each-command]/`
- `profiles/default/workflows/[each-category]/`
- `profiles/default/standards/[each-category]/`
- `profiles/default/docs/command-references/`
