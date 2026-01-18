# Basepoint: profiles/default/workflows/

## Overview

The `workflows/` folder contains reusable workflow templates that are injected into commands. Each workflow is a self-contained unit of work that can be composed into larger command sequences.

## Purpose

Provides reusable building blocks for:
- Project detection and analysis
- Basepoint extraction and caching
- Specification writing and validation
- Implementation and verification
- Human review and checkpoints

## Structure

```
workflows/
├── basepoints/          # Basepoint extraction workflows
├── cleanup/             # Cleanup workflows (product-focused-cleanup)
├── codebase-analysis/   # Codebase analysis workflows
├── common/              # Shared initialization workflows
├── deep-reading/        # Deep code analysis
├── detection/           # Project detection (tech stack, commands)
├── human-review/        # Human review checkpoints
├── implementation/      # Implementation workflows
│   └── verification/    # Verification sub-workflows
├── learning/            # Session learning capture
├── planning/            # Product planning workflows
├── prompting/           # Prompt construction
├── research/            # Web research workflows
├── scope-detection/     # Abstraction layer detection
├── specification/       # Spec writing workflows
└── validation/          # Validation workflows
```

## Key Workflow Categories

### Detection (~7 files)
- `detect-project-profile.md` - Main orchestrator
- `detect-tech-stack.md` - Language/framework detection
- `detect-commands.md` - Build/test/lint detection
- `detect-architecture.md` - Architecture pattern detection
- `detect-security-level.md` - Security assessment

### Basepoints (~3 files)
- `extract-basepoints-knowledge-automatic.md` - Auto extraction
- `extract-basepoints-knowledge-on-demand.md` - On-demand extraction
- `organize-and-cache-basepoints-knowledge.md` - Caching

### Validation (~15 files)
- `orchestrate-validation.md` - Main validator
- `detect-placeholders.md` - Find unreplaced placeholders
- `validate-references.md` - Check file references
- `validate-technology-agnostic.md` - Tech-agnostic check

### Human Review (~5 files)
- `create-checkpoint.md` - Create review checkpoint
- `detect-trade-offs.md` - Find trade-offs
- `detect-contradictions.md` - Find contradictions
- `present-human-decision.md` - Present for review

## Workflow Pattern

```markdown
# Workflow Name

## Purpose
[What this workflow does]

## Inputs
- [Required inputs]

## Outputs
- [Files/variables produced]

## Workflow

### Step 1: [Step Name]
```bash
# Shell commands or logic
```

### Step 2: [Step Name]
[Instructions for AI]
```

## Injection Syntax

Workflows are injected into commands using:
```markdown
{{workflows/category/workflow-name}}
```

Example:
```markdown
{{workflows/detection/detect-project-profile}}
{{workflows/basepoints/extract-basepoints-knowledge-automatic}}
```

## Configuration

`workflow-config.yml` contains:
```yaml
validation:
  enabled: true
  strict_mode: false
  
human_review:
  enabled: true
  checkpoint_frequency: "per_phase"
```

## Output Locations

Workflows write to:
- `agent-os/config/` - Detection results
- `agent-os/output/` - Reports and caches
- `$SPEC_PATH/implementation/cache/` - Spec-specific caches
