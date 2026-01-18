# Task Group 4: Documentation Enhancement

## Overview

**Goal**: Create missing documentation files to improve developer experience.

## Context

The profiles/default/docs/ directory contains reference documentation. This task group adds missing documentation that developers commonly need.

## Standards to Follow

- @agent-os/standards/documentation/standards.md
- @agent-os/standards/global/conventions.md

## Tasks

### Task 4.1: Create TROUBLESHOOTING.md

**File**: `profiles/default/docs/TROUBLESHOOTING.md`

**Structure**:
```markdown
# Troubleshooting Guide

## Common Errors

### "Command not found" Errors
- **Symptom**: Running `/shape-spec` shows "command not found"
- **Cause**: Commands not installed or path incorrect
- **Solution**: 
  1. Verify `agent-os/commands/` exists
  2. Check your AI tool's command configuration
  3. Re-run installation script

### "File not found" Errors
- **Symptom**: Command fails with "cannot find file X"
- **Cause**: Missing prerequisite files
- **Solution**:
  1. Check if you ran commands in order
  2. Verify the file path in error message
  3. Run prerequisite command first

### "Placeholder not replaced" Errors
- **Symptom**: Commands contain `{{PLACEHOLDER}}` text
- **Cause**: Specialization incomplete
- **Solution**:
  1. Run `/deploy-agents` to specialize
  2. Run `/cleanup-agent-os` to verify
  3. Manually replace if needed

### Basepoints Not Loading
- **Symptom**: Commands don't use your patterns
- **Cause**: Basepoints not created or not found
- **Solution**:
  1. Verify `agent-os/basepoints/` exists
  2. Check `headquarter.md` exists
  3. Re-run `/create-basepoints`

## FAQ

### "How do I update basepoints after code changes?"
Run `/update-basepoints-and-redeploy`. This:
1. Detects changed files
2. Updates affected basepoints
3. Re-specializes commands

### "How do I add a new command?"
See CONTRIBUTING.md for detailed guide. Quick steps:
1. Create directory in `commands/[command-name]/single-agent/`
2. Create main command file `[command-name].md`
3. Create phase files `1-[phase].md`, `2-[phase].md`, etc.
4. Register in documentation

### "Why is my spec not using my patterns?"
Check:
1. Basepoints exist and are up-to-date
2. The relevant module has a basepoint
3. The pattern is documented in the basepoint
4. Run `/update-basepoints-and-redeploy` to refresh

### "How do I debug a failing command?"
1. Check the command's prerequisites
2. Verify required files exist
3. Look for error messages in output
4. Check `implementation/cache/` for intermediate files

## Debug Tips

### Check if basepoints are loaded
Look for these in command output:
- "Extracting basepoints knowledge..."
- "Found X relevant basepoints"
- Check `implementation/cache/basepoints-knowledge.md`

### Verify specialization worked
1. Open any command file in `agent-os/commands/`
2. Search for `{{` - should find none (except intentional)
3. Check validation commands are your actual commands

### Trace workflow execution
Commands log their progress. Look for:
- Phase markers: "PHASE 1:", "PHASE 2:", etc.
- Step markers: "Step 1:", "Step 2:", etc.
- Success indicators: "✅", "Done", "Complete"
```

### Task 4.2: Create CONTRIBUTING.md

**File**: `CONTRIBUTING.md` (root of repository)

**Structure**:
```markdown
# Contributing to Geist

Thank you for your interest in contributing to Geist!

## How to Add New Commands

### Directory Structure
```
profiles/default/commands/[command-name]/
└── single-agent/
    ├── [command-name].md      # Main entry point
    ├── 1-[first-phase].md     # Phase 1
    ├── 2-[second-phase].md    # Phase 2
    └── ...
```

### Required Files

1. **Main command file** (`[command-name].md`):
   - Title and description
   - Prerequisites
   - Phase references using `{{PHASE N: @agent-os/commands/...}}`

2. **Phase files** (`N-[phase-name].md`):
   - Clear phase title
   - Step-by-step instructions
   - Workflow references using `{{workflows/...}}`

### Phase Numbering
- Start at 1, increment sequentially
- No gaps allowed
- Last phase should navigate to next command or completion

### Example
See `commands/shape-spec/` for a well-structured example.

## How to Add New Workflows

### Directory Structure
```
profiles/default/workflows/[category]/
└── [workflow-name].md
```

### Categories
- `basepoints/` - Knowledge extraction
- `codebase-analysis/` - Codebase analysis
- `common/` - Shared utilities
- `detection/` - Auto-detection
- `research/` - Web research
- `validation/` - Validation utilities

### Required Sections
1. Title and purpose
2. Inputs (what it expects)
3. Outputs (what it produces)
4. Steps (implementation)

### Registration
After creating a workflow:
1. Reference it from a command using `{{workflows/[category]/[name]}}`
2. Document in WORKFLOW-MAP.md

## Testing Requirements

Before submitting:
1. Verify all workflow references resolve
2. Check phase numbering is sequential
3. Run standards compliance check
4. Test with a sample project

## PR Process

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Run validation checks
5. Submit PR with description of changes
6. Address review feedback

## Code Style

- Use kebab-case for file names
- Include language tags on code blocks
- Follow existing patterns in similar files
- Keep documentation up-to-date
```

### Task 4.3: Update MANIFEST.md

**File**: `MANIFEST.md`

**Updates needed**:
1. Add file counts per directory
2. Add purpose description for each major directory
3. Add navigation guide section
4. Update "Last Updated" date

**New sections to add**:
```markdown
## Directory Overview

| Directory | Files | Purpose |
|-----------|-------|---------|
| `profiles/default/commands/` | ~50 | Command templates |
| `profiles/default/workflows/` | ~40 | Reusable workflows |
| `profiles/default/standards/` | ~12 | Quality standards |
| `profiles/default/docs/` | ~10 | Documentation |
| `scripts/` | 4 | Installation scripts |

## Navigation Guide

**New to Geist?** Start here:
1. `README.md` - Overview and quick start
2. `profiles/default/docs/INSTALLATION-GUIDE.md` - Setup instructions
3. `profiles/default/docs/COMMAND-FLOWS.md` - Command reference

**Adding features?** See:
1. `profiles/default/docs/WORKFLOW-MAP.md` - Visual workflow reference
2. `CONTRIBUTING.md` - How to contribute

**Troubleshooting?** Check:
1. `profiles/default/docs/TROUBLESHOOTING.md` - Common issues
```

### Task 4.4: Update WORKFLOW-MAP.md

**File**: `profiles/default/docs/WORKFLOW-MAP.md`

**Updates needed**:
1. Add "Knowledge Systems" section explaining enriched-knowledge vs library-basepoints
2. Add diagram showing when each knowledge system is created and used
3. Verify all workflows from Task Group 1 are documented
4. Update "Last Updated" date

**New section to add** (after "Workflow Categories"):
```markdown
## Knowledge Systems

Geist has two knowledge systems for library information:

### Timeline
```
adapt-to-product ──────────────────────────────────────────────────────────────►
    │
    └─► Creates: config/enriched-knowledge/
        • library-research.md (generic best practices)
        • security-notes.md (CVEs, vulnerabilities)
        • version-analysis.md (version status)
        
                    create-basepoints ─────────────────────────────────────────►
                        │
                        └─► Creates: basepoints/libraries/
                            • [category]/[library].md (project-specific)
                            • Includes: YOUR usage patterns
                            • Includes: YOUR boundaries
                            • Includes: Security notes (merged)
                            • Includes: Version status (merged)
```

### Usage by Command
| Command | enriched-knowledge | library-basepoints |
|---------|-------------------|-------------------|
| deploy-agents | ✅ Reads | ✅ Reads (if exists) |
| shape-spec | - | ✅ Extracts |
| write-spec | - | ✅ Uses |
| create-tasks | - | ✅ Uses |
| implement-tasks | - | ✅ Uses |
| fix-bug | ✅ Reads | ✅ Uses |
```

### Task 4.5: Update COMMAND-FLOWS.md

**File**: `profiles/default/docs/COMMAND-FLOWS.md`

**Updates needed**:
1. Verify all commands are documented (including fix-bug)
2. Verify all phases are documented for each command
3. Add "Knowledge Sources" subsection to each command showing which knowledge systems it uses
4. Update "Last Updated" date

**Example addition to each command section**:
```markdown
#### Knowledge Sources
- **Basepoints**: ✅ Extracts from `basepoints/`
- **Library Basepoints**: ✅ Extracts from `basepoints/libraries/`
- **Enriched Knowledge**: ❌ Not used
- **Accumulated Knowledge**: ✅ Loads from previous command
```

## Acceptance Criteria

- [ ] `TROUBLESHOOTING.md` exists with common errors, FAQ, and debug tips
- [ ] `CONTRIBUTING.md` exists with clear guidelines for adding commands/workflows
- [ ] `MANIFEST.md` updated with file counts and navigation guide
- [ ] `WORKFLOW-MAP.md` includes knowledge systems section
- [ ] `COMMAND-FLOWS.md` is complete with knowledge sources for each command
- [ ] All documentation follows documentation standards

## Files to Create

1. `profiles/default/docs/TROUBLESHOOTING.md`
2. `CONTRIBUTING.md`

## Files to Modify

1. `MANIFEST.md`
2. `profiles/default/docs/WORKFLOW-MAP.md`
3. `profiles/default/docs/COMMAND-FLOWS.md`

## Validation

After completing this task group:
1. Verify all new files exist
2. Check links in MANIFEST.md work
3. Verify WORKFLOW-MAP.md diagram renders correctly
4. Spot-check COMMAND-FLOWS.md for completeness
