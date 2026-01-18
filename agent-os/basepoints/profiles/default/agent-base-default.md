# Basepoint: profiles/default/

## Overview

The `default/` profile is the main template source for Geist. It contains all command templates, workflows, standards, agents, and documentation that get installed into target projects.

## Purpose

Provides the complete Agent OS template system:
- Command templates for AI-assisted development
- Reusable workflow building blocks
- Coding standards and conventions
- Agent definitions for multi-agent mode
- Comprehensive documentation

## Structure

```
profiles/default/
├── commands/           # Command templates (50+ files)
├── workflows/          # Reusable workflows (80+ files)
├── standards/          # Coding standards (14 files)
├── agents/             # Agent definitions (13 files)
├── docs/               # Documentation (17 files)
├── README.md           # Profile documentation
└── claude-code-skill-template.md  # Skill template
```

## Subfolders

| Folder | Purpose | Files | Basepoint |
|--------|---------|-------|-----------|
| `commands/` | Command templates | ~50 | [agent-base-commands.md](commands/agent-base-commands.md) |
| `workflows/` | Reusable workflows | ~80 | [agent-base-workflows.md](workflows/agent-base-workflows.md) |
| `standards/` | Coding standards | 14 | [agent-base-standards.md](standards/agent-base-standards.md) |
| `agents/` | Agent definitions | 13 | [agent-base-agents.md](agents/agent-base-agents.md) |
| `docs/` | Documentation | 17 | [agent-base-docs.md](docs/agent-base-docs.md) |

## Key Characteristics

### Technology Agnostic
All templates use:
- Generic placeholders (`{{PROJECT_BUILD_COMMAND}}`)
- Conditional logic (`{{IF condition}}...{{ENDIF}}`)
- No hardcoded technology references

### Composable
- Commands inject workflows
- Workflows reference standards
- Agents specialize for tasks

### Hierarchical
- Commands are entry points
- Workflows are building blocks
- Standards are guidelines
- Agents are specialists

## Template Syntax

### Workflow Injection
```markdown
{{workflows/category/workflow-name}}
```

### Standards Reference
```markdown
{{standards/global/*}}
```

### Phase Embedding
```markdown
{{PHASE N: @agent-os/commands/path/file.md}}
```

### Conditional Logic
```markdown
{{IF use_claude_code_subagents}}
  [Multi-agent content]
{{ENDIF}}
```

## Installation

When installed via `scripts/project-install.sh`:
1. Templates are compiled (conditionals resolved)
2. Workflows are injected inline
3. Standards are referenced
4. Output goes to `agent-os/` in target project

## File Statistics

| Category | Count | Percentage |
|----------|-------|------------|
| Commands | ~50 | 29% |
| Workflows | ~80 | 47% |
| Standards | 14 | 8% |
| Agents | 13 | 8% |
| Docs | 17 | 10% |
| **Total** | **~174** | 100% |
