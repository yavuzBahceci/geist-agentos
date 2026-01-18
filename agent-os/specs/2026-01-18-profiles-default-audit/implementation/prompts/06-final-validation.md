# Task Group 6: Final Validation

## Overview

**Goal**: Verify all changes from previous task groups and generate a comprehensive final audit report.

## Context

This is the final task group. It validates that all previous work was completed successfully and documents the final state of the profiles/default directory.

## Standards to Follow

- @agent-os/standards/global/validation.md
- @agent-os/standards/quality/assurance.md
- @agent-os/standards/global/conventions.md

## Tasks

### Task 6.1: Re-run workflow reference validation

**Process**:
1. Re-execute the checks from Task Group 2
2. Verify 0 broken references remain
3. Document any remaining issues

**Expected outcome**: All workflow references resolve to existing files

**Validation commands**:
```bash
# Search for workflow references
grep -r "{{workflows/" profiles/default/commands/ --include="*.md"

# For each reference, verify file exists
# Example: {{workflows/common/extract-basepoints-with-scope-detection}}
# Check: profiles/default/workflows/common/extract-basepoints-with-scope-detection.md exists
```

### Task 6.2: Re-run standards compliance validation

**Process**:
1. Re-execute the checks from Task Group 3
2. Verify all code blocks have language tags
3. Verify no unintentional placeholder text
4. Document any remaining issues

**Validation commands**:
```bash
# Check for code blocks without language tags
grep -rn "^\`\`\`$" profiles/default/ --include="*.md"

# Check for placeholder patterns
grep -rn "\[TODO\]\|\[TBD\]\|FIXME\|XXX" profiles/default/ --include="*.md"
```

### Task 6.3: Verify documentation completeness

**Checklist**:
- [ ] `profiles/default/docs/TROUBLESHOOTING.md` exists
- [ ] `profiles/default/docs/KNOWLEDGE-SYSTEMS.md` exists
- [ ] `CONTRIBUTING.md` exists (in repo root)
- [ ] All docs are linked from README.md or MANIFEST.md
- [ ] All docs have proper structure (H1, sections, etc.)

**Verification**:
```bash
# Check files exist
ls -la profiles/default/docs/TROUBLESHOOTING.md
ls -la profiles/default/docs/KNOWLEDGE-SYSTEMS.md
ls -la CONTRIBUTING.md

# Check links in README
grep -o "\[.*\](.*\.md)" README.md
```

### Task 6.4: Verify knowledge consolidation

**Process**:
1. Check `generate-library-basepoints.md` includes security/version extraction
2. Verify template includes new sections
3. (Optional) Test by generating a library basepoint

**Verification**:
```bash
# Check for security extraction step
grep -n "security" profiles/default/workflows/codebase-analysis/generate-library-basepoints.md

# Check for version extraction step
grep -n "version" profiles/default/workflows/codebase-analysis/generate-library-basepoints.md

# Check template has new sections
grep -n "Security Notes\|Version Status" profiles/default/workflows/codebase-analysis/generate-library-basepoints.md
```

### Task 6.5: Generate final audit report

**Output file**: `agent-os/specs/2026-01-18-profiles-default-audit/implementation/final-audit-report.md`

**Report structure**:
```markdown
# Final Audit Report: Profiles Default Audit & Knowledge Consolidation

## Executive Summary

**Date**: 2026-01-18
**Status**: [Complete/Partial/Incomplete]
**Overall Score**: X/100

## Changes Made

### New Files Created
| File | Purpose |
|------|---------|
| `profiles/default/docs/TROUBLESHOOTING.md` | Common errors and FAQ |
| `profiles/default/docs/KNOWLEDGE-SYSTEMS.md` | Knowledge system documentation |
| `CONTRIBUTING.md` | Contribution guidelines |

### Files Modified
| File | Changes |
|------|---------|
| `README.md` | Enhanced hero, added diagrams |
| `MANIFEST.md` | Added navigation guide |
| `profiles/default/docs/WORKFLOW-MAP.md` | Added knowledge systems section |
| `profiles/default/docs/COMMAND-FLOWS.md` | Added knowledge sources |
| `profiles/default/workflows/codebase-analysis/generate-library-basepoints.md` | Added security/version merge |

### Issues Fixed
| Category | Count | Details |
|----------|-------|---------|
| Broken workflow references | X | [list if any] |
| Missing language tags | X | [list if any] |
| Placeholder text | X | [list if any] |
| Structural issues | X | [list if any] |

## Validation Results

### Workflow References
- **Total references**: X
- **Valid references**: X
- **Broken references**: 0
- **Status**: ✅ Pass

### Standards Compliance
- **Naming conventions**: ✅ Pass
- **Code block tags**: ✅ Pass
- **Placeholder text**: ✅ Pass
- **Markdown structure**: ✅ Pass

### Documentation
- **TROUBLESHOOTING.md**: ✅ Exists
- **KNOWLEDGE-SYSTEMS.md**: ✅ Exists
- **CONTRIBUTING.md**: ✅ Exists
- **All docs linked**: ✅ Yes

### Knowledge Consolidation
- **Security merge**: ✅ Implemented
- **Version merge**: ✅ Implemented
- **Template updated**: ✅ Yes

## Remaining Issues

[List any issues that couldn't be resolved, with recommendations]

## Recommendations for Future

1. [Recommendation 1]
2. [Recommendation 2]
3. [Recommendation 3]

## Appendix: File Inventory

### profiles/default/commands/
[List all command directories with file counts]

### profiles/default/workflows/
[List all workflow directories with file counts]

### profiles/default/docs/
[List all documentation files]

### profiles/default/standards/
[List all standards files]
```

### Task 6.6: Update all "Last Updated" dates

**Files to update**:
1. `README.md` - Update to 2026-01-18
2. `profiles/default/README.md` - Update to 2026-01-18
3. `profiles/default/docs/WORKFLOW-MAP.md` - Update to 2026-01-18
4. `profiles/default/docs/COMMAND-FLOWS.md` - Update to 2026-01-18
5. `MANIFEST.md` - Update to 2026-01-18
6. Any other modified documentation files

**Format**: `**Last Updated**: 2026-01-18`

## Acceptance Criteria

- [ ] All workflow references valid (0 broken)
- [ ] All standards compliance checks pass
- [ ] All required documentation files exist
- [ ] All documentation files are linked
- [ ] Knowledge consolidation verified
- [ ] Final audit report generated
- [ ] All "Last Updated" dates current

## Files to Create

1. `agent-os/specs/2026-01-18-profiles-default-audit/implementation/final-audit-report.md`

## Files to Modify

1. `README.md` (date only)
2. `profiles/default/README.md` (date only)
3. `profiles/default/docs/WORKFLOW-MAP.md` (date only)
4. `profiles/default/docs/COMMAND-FLOWS.md` (date only)
5. `MANIFEST.md` (date only)

## Validation

After completing this task group:
1. Review final audit report
2. Verify all dates are updated
3. Confirm overall project is in good shape
4. Mark spec as complete
