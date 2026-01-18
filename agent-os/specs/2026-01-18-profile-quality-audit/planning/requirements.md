# Requirements: Profile Quality Audit

## Overview

Comprehensive quality audit of `profiles/default/` to ensure professional quality for GitHub open-source release.

---

## 1. Structural Integrity

### 1.1 Workflow Reference Verification
- [ ] All `{{workflows/...}}` references resolve to existing files
- [ ] All `{{PHASE N: ...}}` references resolve to existing phase files
- [ ] No orphaned workflows (workflows that exist but are never referenced)
- [ ] No circular references

### 1.2 Command Phase Ordering
- [ ] All commands have sequential phase numbering (1, 2, 3...)
- [ ] No gaps in phase numbers
- [ ] Phase files exist for all referenced phases

### 1.3 Documentation Cross-References
- [ ] All `[link](path)` references in docs resolve to existing files
- [ ] Table of contents entries match actual sections
- [ ] Command references in docs match actual command names

---

## 2. Missing Components

### 2.1 Missing Workflows (Identified)
- [ ] `{{workflows/planning/gather-product-info}}` - Referenced in WORKFLOW-MAP.md but may not exist
- [ ] Verify all workflows referenced in commands actually exist

### 2.2 Missing Documentation
- [ ] `fix-bug` command needs to be added to command-references/README.md table
- [ ] Check if all commands have corresponding command-reference docs

### 2.3 Missing Standards
- [ ] Verify all standards referenced in commands exist
- [ ] Check for consistency between standards files

---

## 3. Documentation Quality

### 3.1 README.md (Root)
- [ ] Clear value proposition visible immediately
- [ ] Visual diagrams present and accurate
- [ ] Quick start section is actionable
- [ ] Installation instructions are complete
- [ ] Links to detailed docs work

### 3.2 profiles/default/README.md
- [ ] Comprehensive command chain documentation
- [ ] Visual workflows accurate
- [ ] File structure section up-to-date
- [ ] Limitations section honest and helpful

### 3.3 MANIFEST.md
- [ ] Philosophy clearly explained
- [ ] Visuals enhance understanding
- [ ] Credits properly attributed

### 3.4 Command Reference Docs
- [ ] All commands documented in `docs/command-references/`
- [ ] Each doc has consistent structure
- [ ] Visual diagrams present
- [ ] Input/output clearly specified

---

## 4. Professional Quality Standards

### 4.1 Consistency
- [ ] Consistent naming conventions across all files
- [ ] Consistent formatting in markdown files
- [ ] Consistent visual diagram style
- [ ] Consistent terminology (e.g., "basepoints" not "base points")

### 4.2 Code Quality
- [ ] No TODO/FIXME comments in production files
- [ ] No placeholder text that should be replaced
- [ ] No debug/test code left in

### 4.3 Visual Appeal
- [ ] ASCII diagrams are well-formatted
- [ ] Consistent box characters used
- [ ] Proper alignment in tables
- [ ] Clear visual hierarchy

---

## 5. GitHub Presentation

### 5.1 Repository Structure
- [ ] Clear folder organization
- [ ] Appropriate .gitignore
- [ ] LICENSE file present and correct
- [ ] Contributing guidelines (if needed)

### 5.2 README First Impression
- [ ] Value proposition in first 3 lines
- [ ] Visual diagram near top
- [ ] Quick start within first scroll
- [ ] Professional tone

### 5.3 Documentation Navigation
- [ ] Clear path from README to detailed docs
- [ ] Consistent navigation between docs
- [ ] Table of contents in long documents

---

## 6. Technical Accuracy

### 6.1 Command Flows
- [ ] COMMAND-FLOWS.md matches actual command implementations
- [ ] WORKFLOW-MAP.md matches actual workflow structure
- [ ] Phase descriptions match phase file content

### 6.2 File Paths
- [ ] PATH-REFERENCE-GUIDE.md is accurate
- [ ] Example paths in docs are valid
- [ ] Output locations documented correctly

### 6.3 Placeholder Documentation
- [ ] All `{{PLACEHOLDER}}` types documented
- [ ] Replacement process explained
- [ ] Examples provided

---

## 7. New Features Integration

### 7.1 Context Enrichment
- [ ] Library basepoints workflow documented
- [ ] Knowledge accumulation explained
- [ ] Narrow focus + expand knowledge strategy documented

### 7.2 fix-bug Command
- [ ] Command fully documented
- [ ] Added to all relevant indexes
- [ ] Phases documented
- [ ] Integration with other commands explained

---

## Success Criteria

1. **Zero broken references** - All workflow/phase/doc references resolve
2. **Complete documentation** - Every command has reference docs
3. **Visual consistency** - All diagrams use same style
4. **Professional quality** - Ready for public GitHub release
5. **Developer-friendly** - New user can understand value within 2 minutes
6. **Accurate** - Documentation matches implementation

---

## Scope

### In Scope
- `profiles/default/` - All files
- Root documentation (README.md, MANIFEST.md)
- `scripts/` - Documentation accuracy

### Out of Scope
- Actual command implementation changes
- New feature development
- Performance optimization
