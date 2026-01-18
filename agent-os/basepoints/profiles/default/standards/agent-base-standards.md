# Basepoint: profiles/default/standards/

## Overview

The `standards/` folder contains coding standards and conventions that are referenced by commands and workflows. These standards guide AI assistants in generating consistent, high-quality code.

## Purpose

Provides guidelines for:
- Code style and conventions
- Documentation requirements
- Error handling patterns
- Testing approaches
- Quality assurance

## Structure

```
standards/
├── documentation/
│   └── standards.md           # Documentation standards
├── global/
│   ├── codebase-analysis.md   # How to analyze codebases
│   ├── coding-style.md        # Code style guidelines
│   ├── commenting.md          # Comment conventions
│   ├── conventions.md         # General conventions + output rules
│   ├── enriched-knowledge-templates.md  # Knowledge templates
│   ├── error-handling.md      # Error handling patterns
│   ├── project-profile-schema.md  # Profile schema
│   ├── tech-stack.md          # Tech stack documentation
│   ├── validation-commands.md # Validation command patterns
│   └── validation.md          # Validation guidelines
├── process/
│   └── development-workflow.md  # Development process
├── quality/
│   └── assurance.md           # QA guidelines
└── testing/
    └── test-writing.md        # Test writing guidelines
```

## Key Standards

### conventions.md (Critical)
Contains the **Agent OS Output Conventions**:
```markdown
CRITICAL: All outputs MUST go to `agent-os/` directory

- Specs: agent-os/specs/[date]-[name]/
- Product docs: agent-os/product/
- Basepoints: agent-os/basepoints/
- Config: agent-os/config/
- Reports/outputs: agent-os/output/
- Plans: agent-os/output/plans/
- Caches: agent-os/specs/[spec]/implementation/cache/
```

### coding-style.md
- Consistent naming conventions
- File organization
- Import ordering
- Code formatting

### error-handling.md
- Error types and handling
- Logging patterns
- Recovery strategies

### validation.md
- Validation approaches
- Test coverage requirements
- CI/CD integration

## Injection Syntax

Standards are referenced using:
```markdown
{{standards/global/*}}           # All global standards
{{standards/global/conventions}} # Specific standard
{{standards/testing/*}}          # All testing standards
```

## Technology Agnosticism

Standards are intentionally **technology-agnostic**:
- No specific language references
- No framework-specific patterns
- Placeholders for project-specific values
- Generic patterns that apply to any stack

## Standard Pattern

```markdown
## [Standard Category]

- **[Principle]**: [Description]
- **[Principle]**: [Description]

### Examples

[Generic examples that work for any tech stack]
```

## File Count

| Category | Files | Purpose |
|----------|-------|---------|
| `global/` | 10 | Universal standards |
| `documentation/` | 1 | Doc standards |
| `process/` | 1 | Workflow standards |
| `quality/` | 1 | QA standards |
| `testing/` | 1 | Test standards |
| **Total** | **14** | |
