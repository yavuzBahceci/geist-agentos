# Geist - Headquarter

## Project Overview

**Geist** is a universal Agent OS that makes AI coding assistants project-aware. It extends the original Agent OS concept to work with any project type by automatically detecting tech stacks, documenting codebase patterns, and specializing AI commands.

## Quick Reference

| Attribute | Value |
|-----------|-------|
| **Name** | Geist |
| **Type** | Developer Tool / AI Agent Framework |
| **Language** | Shell (Bash) + Markdown |
| **License** | MIT |
| **Version** | 2.1.1 |

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                      GEIST ARCHITECTURE                      │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│  scripts/                                                    │
│  └── Installation & automation scripts                       │
└─────────────────────────────────────────────────────────────┘
                              │
                              │ installs from
                              ▼
┌─────────────────────────────────────────────────────────────┐
│  profiles/default/                                           │
│  ├── commands/      → Command templates                      │
│  ├── workflows/     → Reusable workflow templates            │
│  ├── standards/     → Coding standards                       │
│  ├── agents/        → Agent definitions                      │
│  └── docs/          → Documentation                          │
└─────────────────────────────────────────────────────────────┘
                              │
                              │ compiles to
                              ▼
┌─────────────────────────────────────────────────────────────┐
│  target-project/agent-os/                                    │
│  ├── commands/      → Compiled commands                      │
│  ├── workflows/     → Compiled workflows                     │
│  ├── standards/     → Compiled standards                     │
│  ├── product/       → Product documentation                  │
│  ├── basepoints/    → Codebase documentation                 │
│  └── output/        → Generated outputs                      │
└─────────────────────────────────────────────────────────────┘
```

## Directory Structure

```
Geist/
├── scripts/              # Installation scripts
│   ├── project-install.sh
│   ├── project-update.sh
│   └── common-functions.sh
│
├── profiles/             # Template profiles
│   └── default/          # Default profile
│       ├── commands/     # 50+ command templates
│       ├── workflows/    # 80+ workflow templates
│       ├── standards/    # 14 standard files
│       ├── agents/       # 13 agent definitions
│       └── docs/         # 17 documentation files
│
├── config.yml            # Global configuration
├── README.md             # Project documentation
├── MANIFEST.md           # Philosophy and motivation
└── LICENSE               # MIT License
```

## Basepoint Index

| Path | Basepoint | Description |
|------|-----------|-------------|
| `scripts/` | [agent-base-scripts.md](scripts/agent-base-scripts.md) | Installation scripts |
| `profiles/` | [agent-base-profiles.md](profiles/agent-base-profiles.md) | Template profiles |
| `profiles/default/` | [agent-base-default.md](profiles/default/agent-base-default.md) | Default profile |
| `profiles/default/commands/` | [agent-base-commands.md](profiles/default/commands/agent-base-commands.md) | Command templates |
| `profiles/default/workflows/` | [agent-base-workflows.md](profiles/default/workflows/agent-base-workflows.md) | Workflow templates |
| `profiles/default/standards/` | [agent-base-standards.md](profiles/default/standards/agent-base-standards.md) | Coding standards |
| `profiles/default/agents/` | [agent-base-agents.md](profiles/default/agents/agent-base-agents.md) | Agent definitions |
| `profiles/default/docs/` | [agent-base-docs.md](profiles/default/docs/agent-base-docs.md) | Documentation |

## Command Chains

### Setup Chain (One-Time)
```
/adapt-to-product → /create-basepoints → /deploy-agents
```

### Development Chain (Per Feature)
```
/shape-spec → /write-spec → /create-tasks → /implement-tasks
```

### Maintenance
```
/update-basepoints-and-redeploy
/cleanup-agent-os
```

## Key Patterns

### 1. Template Compilation
Templates use placeholders and conditionals that get resolved during installation:
```markdown
{{IF use_claude_code_subagents}}
  [Multi-agent content]
{{ENDIF}}

{{workflows/category/workflow-name}}
```

### 2. Technology Agnosticism
All templates are generic and work with any tech stack. Project-specific values come from detection or configuration.

### 3. Hierarchical Documentation
Basepoints form a hierarchy from project overview (headquarter) down to individual modules.

### 4. Output Isolation
All generated content goes to `agent-os/` directory, keeping the project root clean.

## Product Documentation

| Document | Location |
|----------|----------|
| Mission | `agent-os/product/mission.md` |
| Roadmap | `agent-os/product/roadmap.md` |
| Tech Stack | `agent-os/product/tech-stack.md` |

## Configuration

| File | Purpose |
|------|---------|
| `config.yml` | Global defaults |
| `agent-os/config.yml` | Installation config |
| `agent-os/config/project-profile.yml` | Detected profile |

## Statistics

| Category | Count |
|----------|-------|
| Scripts | 4 |
| Command templates | ~50 |
| Workflow templates | ~80 |
| Standards | 14 |
| Agents | 13 |
| Documentation | 17 |
| **Total files** | **~178** |
