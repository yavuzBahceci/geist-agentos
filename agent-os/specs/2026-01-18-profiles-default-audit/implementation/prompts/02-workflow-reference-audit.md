# Task Group 2: Workflow Reference Audit

## Overview

**Goal**: Validate all workflow references resolve to existing files and identify any broken references or orphaned workflows.

## Context

Commands reference workflows using the `{{workflows/...}}` syntax. This audit ensures:
1. All references point to existing files
2. Phase numbers are sequential
3. No orphaned workflows exist

## Standards to Follow

- @agent-os/standards/global/conventions.md
- @agent-os/standards/global/codebase-analysis.md
- @agent-os/standards/quality/assurance.md

## Tasks

### Task 2.1: Audit adapt-to-product command

**Directory**: `profiles/default/commands/adapt-to-product/single-agent/`

**Checklist**:
- [ ] List all `{{workflows/...}}` references in all phase files
- [ ] Verify each reference resolves to existing file in `profiles/default/workflows/`
- [ ] Verify phase files are numbered 1-8 sequentially
- [ ] Document any broken references

### Task 2.2: Audit plan-product command

**Directory**: `profiles/default/commands/plan-product/single-agent/`

**Checklist**:
- [ ] List all `{{workflows/...}}` references
- [ ] Verify each reference resolves to existing file
- [ ] Verify phase numbering is sequential
- [ ] Document any broken references

### Task 2.3: Audit create-basepoints command

**Directory**: `profiles/default/commands/create-basepoints/single-agent/`

**Checklist**:
- [ ] List all `{{workflows/...}}` references
- [ ] Verify each reference resolves to existing file
- [ ] Verify phase files are numbered 1-8 sequentially
- [ ] Specifically verify Phase 8 (`8-generate-library-basepoints.md`) exists and references `workflows/codebase-analysis/generate-library-basepoints.md`
- [ ] Document any broken references

### Task 2.4: Audit deploy-agents command

**Directory**: `profiles/default/commands/deploy-agents/single-agent/`

**Checklist**:
- [ ] List all `{{workflows/...}}` references
- [ ] Verify each reference resolves to existing file
- [ ] Verify phase files are numbered 1-14 sequentially
- [ ] Document any broken references

### Task 2.5: Audit spec commands (shape-spec, write-spec, create-tasks)

**Directories**: 
- `profiles/default/commands/shape-spec/single-agent/`
- `profiles/default/commands/write-spec/single-agent/`
- `profiles/default/commands/create-tasks/single-agent/`

**Checklist**:
- [ ] Verify `extract-basepoints-with-scope-detection.md` exists in `workflows/common/`
- [ ] Verify `extract-library-basepoints-knowledge.md` exists in `workflows/common/`
- [ ] Verify `accumulate-knowledge.md` exists in `workflows/common/`
- [ ] List all other workflow references and verify they exist
- [ ] Document any broken references

### Task 2.6: Audit implementation commands (implement-tasks, orchestrate-tasks, fix-bug)

**Directories**:
- `profiles/default/commands/implement-tasks/single-agent/`
- `profiles/default/commands/orchestrate-tasks/`
- `profiles/default/commands/fix-bug/single-agent/`

**Checklist**:
- [ ] List all `{{workflows/...}}` references
- [ ] Verify each reference resolves to existing file
- [ ] For fix-bug: verify all 6 phases (1-6) exist
- [ ] Document any broken references

### Task 2.7: Identify orphaned workflows

**Process**:
1. List all `.md` files in `profiles/default/workflows/` recursively
2. For each workflow file, search all command files for references
3. A workflow is "orphaned" if no command references it
4. Document all orphaned workflows

**Note**: Some workflows may be called by other workflows, not directly by commands. Check workflow-to-workflow references too.

### Task 2.8: Create audit report

**Output file**: `agent-os/specs/2026-01-18-profiles-default-audit/implementation/cache/workflow-audit-report.md`

**Report structure**:
```markdown
# Workflow Reference Audit Report

## Summary
- Total commands audited: X
- Total workflow references found: X
- Broken references: X
- Orphaned workflows: X

## Broken References
| Command | Phase | Reference | Issue |
|---------|-------|-----------|-------|
| ... | ... | ... | ... |

## Orphaned Workflows
| Workflow Path | Possible Reason |
|---------------|-----------------|
| ... | ... |

## Phase Numbering Issues
| Command | Issue |
|---------|-------|
| ... | ... |

## Recommendations
1. ...
2. ...
```

## Acceptance Criteria

- [ ] All commands audited (adapt-to-product, plan-product, create-basepoints, deploy-agents, shape-spec, write-spec, create-tasks, implement-tasks, orchestrate-tasks, fix-bug)
- [ ] All broken references documented
- [ ] All orphaned workflows documented
- [ ] Audit report generated at specified location
- [ ] Phase numbering verified for all commands

## Files to Create

1. `agent-os/specs/2026-01-18-profiles-default-audit/implementation/cache/workflow-audit-report.md`

## Validation

After completing this task group:
1. Review the audit report
2. Decide which broken references need immediate fixing vs documentation
3. Decide which orphaned workflows should be removed vs kept
