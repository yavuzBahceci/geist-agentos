# Basepoint: profiles/default/commands/

## Overview

The `commands/` folder contains all command templates that get installed into target projects. Each command is a multi-phase workflow that guides AI assistants through structured tasks.

## Purpose

Provides command templates for:
- **Setup Chain**: adapt-to-product → create-basepoints → deploy-agents
- **Development Chain**: shape-spec → write-spec → create-tasks → implement-tasks
- **Maintenance**: update-basepoints-and-redeploy, cleanup-agent-os

## Structure

```
commands/
├── adapt-to-product/      # Extract product info from codebase
├── plan-product/          # Plan new product from scratch
├── create-basepoints/     # Document codebase patterns
├── deploy-agents/         # Specialize commands
├── shape-spec/            # Gather requirements
├── write-spec/            # Write specification
├── create-tasks/          # Break into tasks
├── implement-tasks/       # Implement code
├── orchestrate-tasks/     # Multi-agent orchestration
├── update-basepoints-and-redeploy/  # Sync after changes
├── cleanup-agent-os/      # Validate deployment
└── improve-skills/        # Improve Claude Code skills
```

## Command Pattern

Each command follows this structure:
```
command-name/
├── single-agent/
│   ├── command-name.md      # Main entry point
│   ├── 1-phase-one.md       # Phase 1
│   ├── 2-phase-two.md       # Phase 2
│   └── ...
└── multi-agent/             # (optional)
    └── command-name.md      # Multi-agent variant
```

## Key Patterns

### 1. Phase Sequencing
```markdown
Carefully read and execute the instructions in the following files IN SEQUENCE.

{{PHASE 1: @agent-os/commands/command/1-phase.md}}
{{PHASE 2: @agent-os/commands/command/2-phase.md}}
```

### 2. Workflow Injection
```markdown
## Step N: Execute Workflow
{{workflows/category/workflow-name}}
```

### 3. Standards Reference
```markdown
## Standards to Follow
{{standards/global/*}}
```

### 4. Conditional Logic
```markdown
{{IF use_claude_code_subagents}}
  [Multi-agent content]
{{ENDIF}}
```

## Commands Summary

| Command | Phases | Purpose |
|---------|--------|---------|
| `/adapt-to-product` | 8 | Extract product info, cleanup |
| `/plan-product` | 5 | Plan new product |
| `/create-basepoints` | 7 | Document codebase |
| `/deploy-agents` | 13 | Specialize templates |
| `/shape-spec` | 2 | Gather requirements |
| `/write-spec` | 1 | Write specification |
| `/create-tasks` | 2 | Break into tasks |
| `/implement-tasks` | 3 | Implement code |
| `/orchestrate-tasks` | 1 | Generate prompts |
| `/update-basepoints-and-redeploy` | 8 | Sync changes |
| `/cleanup-agent-os` | 6 | Validate deployment |

## File Dependencies

Commands depend on:
- `workflows/` - Reusable workflow templates
- `standards/` - Coding standards
- `agents/` - Agent definitions (for multi-agent)

## Output Locations

Commands write to:
- `agent-os/specs/` - Specifications
- `agent-os/product/` - Product documentation
- `agent-os/basepoints/` - Codebase documentation
- `agent-os/output/` - Reports and caches
