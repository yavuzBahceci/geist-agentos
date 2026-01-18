# Task Group 3: Standards Compliance

## Overview

**Goal**: Ensure all files in `profiles/default/` follow conventions and are clean.

## Context

The profiles/default directory contains templates that get installed into user projects. These files should be exemplary in following standards.

## Standards to Follow

- @agent-os/standards/global/conventions.md
- @agent-os/standards/global/coding-style.md
- @agent-os/standards/global/commenting.md
- @agent-os/standards/quality/assurance.md

## Tasks

### Task 3.1: Audit naming conventions

**Scope**: All files in `profiles/default/`

**Rules to check**:
- All directories use kebab-case (e.g., `single-agent/`, not `singleAgent/`)
- All markdown files use kebab-case (e.g., `my-workflow.md`, not `myWorkflow.md`)
- Exception: README.md, MANIFEST.md, CONTRIBUTING.md (uppercase by convention)

**Process**:
1. List all files and directories
2. Check each name against kebab-case pattern: `^[a-z0-9]+(-[a-z0-9]+)*(\.[a-z]+)?$`
3. Document exceptions and violations

**Output**: List of files not following convention with recommendation (fix or document as exception)

### Task 3.2: Audit code block language tags

**Scope**: All `.md` files in `profiles/default/`

**Rules to check**:
- Every code block (```) must have a language tag
- Common tags: `bash`, `markdown`, `yaml`, `json`, `typescript`, `javascript`
- Pseudo-code can use `text` or `plaintext`

**Process**:
1. Search for code blocks without language tags: ` ```\n` (backticks followed by newline)
2. For each found, determine appropriate language tag
3. Add the language tag

**Output**: Count of code blocks fixed, list of files modified

### Task 3.3: Audit placeholder text

**Scope**: All files in `profiles/default/`

**Patterns to search**:
- `{{PLACEHOLDER}}` - intentional placeholders (should be documented)
- `[TODO]`, `[TBD]`, `TODO:`, `TBD:` - incomplete items
- `FIXME`, `XXX` - issues to fix
- `[INSERT ...]` - missing content
- `...` at start of line (truncation markers that shouldn't be in final docs)

**Process**:
1. Search for each pattern
2. For intentional placeholders (like `{{PROJECT_BUILD_COMMAND}}`): verify they're documented
3. For unintentional placeholders: fix or remove
4. Document all findings

**Output**: List of all placeholders found with status (intentional/fixed/needs-attention)

### Task 3.4: Audit markdown structure

**Scope**: All `.md` files in `profiles/default/`

**Rules to check**:
1. Each file has exactly one H1 (`#`) heading
2. Heading hierarchy is proper (H1 > H2 > H3, no skipping levels)
3. No orphan headings (heading with no content before next heading)

**Process**:
1. Parse each markdown file
2. Check for multiple H1 headings
3. Check for heading level skips (e.g., H1 directly to H3)
4. Document issues

**Output**: List of structural issues per file

### Task 3.5: Create standards compliance report

**Output file**: `agent-os/specs/2026-01-18-profiles-default-audit/implementation/cache/standards-compliance-report.md`

**Report structure**:
```markdown
# Standards Compliance Report

## Summary
- Files audited: X
- Naming convention issues: X
- Code block issues fixed: X
- Placeholder issues: X
- Structural issues: X
- Overall compliance score: X%

## Naming Convention Issues
| File/Directory | Issue | Recommendation |
|----------------|-------|----------------|
| ... | ... | ... |

## Code Block Fixes
| File | Line | Language Added |
|------|------|----------------|
| ... | ... | ... |

## Placeholder Issues
| File | Pattern | Status |
|------|---------|--------|
| ... | ... | Intentional/Fixed/Needs-attention |

## Structural Issues
| File | Issue |
|------|-------|
| ... | ... |

## Compliance Score Calculation
- Naming: X/Y files compliant
- Code blocks: X/Y blocks have tags
- Placeholders: X/Y are intentional
- Structure: X/Y files have proper structure
- **Overall: X%**
```

## Acceptance Criteria

- [ ] All files checked for naming convention compliance
- [ ] All code blocks have language tags (or documented exceptions)
- [ ] No unintentional placeholder text remains
- [ ] All markdown files have proper structure
- [ ] Standards compliance report generated

## Files to Create

1. `agent-os/specs/2026-01-18-profiles-default-audit/implementation/cache/standards-compliance-report.md`

## Files to Potentially Modify

Any file in `profiles/default/` that has:
- Missing code block language tags
- Unintentional placeholder text
- Structural issues

## Validation

After completing this task group:
1. Review the compliance report
2. Verify code blocks have language tags by spot-checking
3. Search for placeholder patterns to confirm none remain
