# Requirements: Template Quality & Documentation Improvement

## Overview

This specification covers a comprehensive audit and improvement of the `profiles/default/` templates to ensure they are complete, well-documented, and professionally presented for GitHub.

---

## Analysis Summary

### Current State Assessment

#### ✅ What's Working Well

1. **Workflow References**: All `{{workflows/...}}` references resolve to existing files
   - 100+ workflow files exist
   - No broken workflow references found (only `...` placeholder patterns)

2. **Documentation Structure**: Comprehensive docs exist
   - `COMMAND-FLOWS.md` - Detailed command documentation
   - `WORKFLOW-MAP.md` - Visual workflow reference
   - `PATH-REFERENCE-GUIDE.md` - Path conventions
   - `INSTALLATION-GUIDE.md` - Setup instructions
   - `command-references/` - 13 per-command visual guides

3. **Command Coverage**: All commands have:
   - Main command file
   - Phase files (numbered)
   - Multi-agent variants where applicable

4. **README Quality**: Both root and profiles/default READMEs are:
   - Well-structured with ASCII diagrams
   - Clear about what Geist does and doesn't do
   - Honest about limitations

#### ⚠️ Areas Needing Improvement

1. **Missing Command Reference**: `improve-skills` command exists but has no command-reference doc

2. **Placeholder Documentation**: Some `{{...}}` placeholder patterns could be better documented

3. **Visual Consistency**: Some docs have ASCII diagrams, others don't

4. **GitHub Presentation**:
   - No badges for build status, version, etc.
   - No screenshots/GIFs of actual usage
   - No "Why Geist?" quick comparison section in root README
   - MANIFEST.md is philosophical but could link better to practical docs

5. **Onboarding Experience**:
   - No "5-minute quickstart" section
   - No video/GIF demonstrations
   - No FAQ section

6. **Standards Documentation**:
   - Standards exist but aren't well-indexed
   - No overview of what standards cover

---

## Requirements

### 1. Missing Documentation

#### 1.1 Create `improve-skills` Command Reference
- **File**: `profiles/default/docs/command-references/improve-skills.md`
- **Content**: Visual guide matching other command references
- **Priority**: High

#### 1.2 Create Standards Overview
- **File**: `profiles/default/docs/STANDARDS-OVERVIEW.md`
- **Content**: Index of all standards with descriptions
- **Priority**: Medium

### 2. GitHub Presentation Improvements

#### 2.1 Root README Enhancements
- Add version badge
- Add license badge (already exists)
- Add "Quick Start in 5 Minutes" section at top
- Add comparison table: "Before/After Geist"
- Add link to demo/video if available
- **Priority**: High

#### 2.2 Add Visual Assets
- Create `docs/images/` directory for diagrams
- Consider adding Mermaid diagrams for better rendering
- **Priority**: Medium

#### 2.3 MANIFEST.md Improvements
- Add "Next Steps" section linking to practical docs
- Add "Quick Links" at top
- **Priority**: Low

### 3. Documentation Consistency

#### 3.1 Ensure All Commands Have Visual Guides
- Audit all command-references for completeness
- Add missing diagrams where helpful
- **Priority**: Medium

#### 3.2 Standardize Documentation Format
- Ensure consistent heading structure
- Ensure consistent diagram style
- **Priority**: Low

### 4. Developer Experience

#### 4.1 Add FAQ Section
- **File**: `profiles/default/docs/FAQ.md` or section in README
- **Content**:
  - "How long does setup take?"
  - "What if detection is wrong?"
  - "How do I update basepoints?"
  - "Can I use this with [X IDE]?"
- **Priority**: Medium

#### 4.2 Add Troubleshooting Guide
- **File**: `profiles/default/docs/TROUBLESHOOTING.md`
- **Content**: Common issues and solutions
- **Priority**: Medium

### 5. Code Quality Audit

#### 5.1 Verify All Cross-References
- Check all `@agent-os/` references are valid
- Check all internal links in docs work
- **Priority**: High

#### 5.2 Verify Placeholder Consistency
- Ensure all `{{PLACEHOLDER}}` patterns are documented
- Ensure consistent naming conventions
- **Priority**: Medium

#### 5.3 Remove Dead Code/Comments
- Audit for TODO comments that should be resolved
- Remove any deprecated content
- **Priority**: Low

---

## Implementation Order

1. **Phase 1: Critical Fixes** (High Priority)
   - Create missing `improve-skills` command reference
   - Verify all cross-references work
   - Add quick start section to root README

2. **Phase 2: Documentation Enhancement** (Medium Priority)
   - Create Standards Overview
   - Add FAQ section
   - Add Troubleshooting guide
   - Ensure visual consistency

3. **Phase 3: Polish** (Low Priority)
   - MANIFEST.md improvements
   - Standardize all documentation format
   - Add visual assets

---

## Success Criteria

- [ ] All commands have corresponding command-reference docs
- [ ] No broken internal references
- [ ] Root README has clear "5-minute quickstart"
- [ ] FAQ addresses common questions
- [ ] Standards are indexed and discoverable
- [ ] Documentation follows consistent format
- [ ] GitHub presentation is professional and inviting

---

## Notes

### What's NOT in Scope
- Adding new commands or workflows
- Changing existing command behavior
- Major refactoring of template structure

### Assumptions
- Current workflow/command structure is correct
- Existing documentation is accurate (just needs enhancement)
- ASCII diagrams are preferred over images for portability

---

## Appendix: Audit Results

### Workflow Reference Check
```
Referenced workflows: 80+
Existing workflows: 100+
Missing workflows: 0 (only placeholder patterns like "...")
```

### Command Reference Check
```
Commands: 13 total
- adapt-to-product ✅
- plan-product ✅
- create-basepoints ✅
- deploy-agents ✅
- cleanup-agent-os ✅
- update-basepoints-and-redeploy ✅
- shape-spec ✅
- write-spec ✅
- create-tasks ✅
- implement-tasks ✅
- orchestrate-tasks ✅
- fix-bug ✅
- improve-skills ❌ (missing command-reference)
```

### Documentation Files
```
profiles/default/docs/
├── COMMAND-FLOWS.md ✅
├── WORKFLOW-MAP.md ✅
├── INSTALLATION-GUIDE.md ✅
├── PATH-REFERENCE-GUIDE.md ✅
├── REFACTORING-GUIDELINES.md ✅
├── command-references/ (13 files) ✅
└── FAQ.md ❌ (missing)
└── TROUBLESHOOTING.md ❌ (missing)
└── STANDARDS-OVERVIEW.md ❌ (missing)
```

---

**Last Updated**: 2026-01-18
