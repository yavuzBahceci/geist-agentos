# Basepoint: profiles/default/docs/

## Overview

The `docs/` folder contains documentation for Geist, including command references, installation guides, and best practices.

## Purpose

Provides documentation for:
- Command usage and workflows
- Installation and setup
- Path conventions
- Refactoring guidelines
- Technology-agnostic best practices

## Structure

```
docs/
├── COMMAND-FLOWS.md              # Detailed command documentation
├── INSTALLATION-GUIDE.md         # Step-by-step setup
├── PATH-REFERENCE-GUIDE.md       # File path conventions
├── REFACTORING-GUIDELINES.md     # Refactoring best practices
├── TECHNOLOGY-AGNOSTIC-BEST-PRACTICES.md  # Tech-agnostic patterns
└── command-references/           # Visual command guides
    ├── README.md                 # Command overview
    ├── adapt-to-product.md       # /adapt-to-product reference
    ├── plan-product.md           # /plan-product reference
    ├── create-basepoints.md      # /create-basepoints reference
    ├── deploy-agents.md          # /deploy-agents reference
    ├── shape-spec.md             # /shape-spec reference
    ├── write-spec.md             # /write-spec reference
    ├── create-tasks.md           # /create-tasks reference
    ├── implement-tasks.md        # /implement-tasks reference
    ├── orchestrate-tasks.md      # /orchestrate-tasks reference
    ├── update-basepoints-and-redeploy.md  # Update reference
    └── cleanup-agent-os.md       # Cleanup reference
```

## Key Documents

### COMMAND-FLOWS.md
Comprehensive documentation of all commands:
- Phase breakdowns
- Input/output specifications
- Workflow integrations
- Knowledge integration patterns

### command-references/
Visual guides with ASCII diagrams:
- Quick overview boxes
- Phase flow diagrams
- Input/output tables
- Tips and best practices

### INSTALLATION-GUIDE.md
Step-by-step installation:
- Prerequisites
- Installation options
- Configuration
- Troubleshooting

### PATH-REFERENCE-GUIDE.md
File path conventions:
- Template paths (`profiles/default/`)
- Installed paths (`agent-os/`)
- Output paths (`agent-os/output/`)

### TECHNOLOGY-AGNOSTIC-BEST-PRACTICES.md
Guidelines for keeping templates generic:
- Placeholder usage
- Conditional logic
- Avoiding tech-specific references

## Documentation Pattern

Command references follow:
```markdown
# /command-name Command Reference

> **Purpose**: [One-line description]

## Quick Overview
[ASCII diagram]

## Visual Flow
[Phase diagram]

## Phase Details
[Detailed breakdown]

## Inputs & Outputs
[Table]

## Next Command
[What to run next]

## Tips
[Best practices]
```

## File Count

| Category | Files |
|----------|-------|
| Main docs | 5 |
| Command references | 12 |
| **Total** | **17** |
