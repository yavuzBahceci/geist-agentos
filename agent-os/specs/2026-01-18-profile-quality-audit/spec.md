# Specification: Profile Quality Audit

## Executive Summary

This spec defines a comprehensive quality audit of `profiles/default/` to ensure professional quality for GitHub open-source release. The audit will identify and fix issues with references, documentation, and presentation.

---

## Issues Identified

### 1. Missing Documentation References

#### 1.1 `fix-bug` Command Missing from README Table
**Location**: `profiles/default/docs/command-references/README.md`
**Issue**: The `fix-bug` command exists (`fix-bug.md`) but is not listed in the command tables
**Fix**: Add `fix-bug` to the Development Commands table and visual diagram

#### 1.2 `fix-bug` Missing from Visual Diagram
**Location**: `profiles/default/docs/command-references/README.md`
**Issue**: The command overview visual doesn't include `fix-bug`
**Fix**: Add `fix-bug` to the MAINTENANCE COMMANDS section of the visual

---

### 2. Documentation Improvements

#### 2.1 Root README.md Enhancements
**Current State**: Good but could be more impactful for GitHub visitors
**Improvements**:
- Add badges (license, version, etc.)
- Add a "Why Geist?" section with clear value proposition
- Add a quick visual showing before/after
- Improve quick start visibility

#### 2.2 MANIFEST.md Enhancements
**Current State**: Good philosophy explanation
**Improvements**:
- Ensure all visuals render correctly
- Add link to detailed documentation

---

### 3. Visual Consistency

#### 3.1 ASCII Diagram Standards
**Issue**: Some diagrams use different box characters
**Fix**: Standardize on:
- `┌─────┐` for top borders
- `│     │` for sides
- `└─────┘` for bottom borders
- `═══════` for section separators
- `▶` for arrows

---

### 4. Cross-Reference Verification

All workflow references verified as existing:
- ✅ `{{workflows/detection/detect-project-profile}}`
- ✅ `{{workflows/research/research-orchestrator}}`
- ✅ `{{workflows/planning/gather-product-info}}`
- ✅ `{{workflows/cleanup/product-focused-cleanup}}`
- ✅ `{{workflows/common/extract-basepoints-with-scope-detection}}`
- ✅ `{{workflows/common/extract-library-basepoints-knowledge}}`
- ✅ `{{workflows/common/accumulate-knowledge}}`
- ✅ `{{workflows/codebase-analysis/generate-library-basepoints}}`

---

## Implementation Tasks

### Task Group 1: Fix Missing `fix-bug` References

1.1. Update `docs/command-references/README.md`:
   - Add `fix-bug` to Development Commands table
   - Add `fix-bug` to visual diagram
   - Add to "See Also" sections where relevant

### Task Group 2: Enhance Root Documentation

2.1. Update root `README.md`:
   - Add GitHub badges
   - Improve value proposition visibility
   - Add "Why Geist?" section

2.2. Update `MANIFEST.md`:
   - Verify all visuals
   - Add navigation links

### Task Group 3: Documentation Polish

3.1. Review all command-reference docs for consistency
3.2. Ensure all "See Also" sections are complete
3.3. Verify all internal links work

### Task Group 4: Visual Improvements

4.1. Add visual diagram to root README showing command flow
4.2. Ensure consistent ASCII art style across all docs

---

## Success Criteria

- [ ] `fix-bug` command appears in all relevant documentation indexes
- [ ] All internal documentation links resolve correctly
- [ ] Root README provides clear value proposition within first 3 lines
- [ ] Visual diagrams are consistent in style
- [ ] New developer can understand Geist's value within 2 minutes of reading README
- [ ] All commands have corresponding reference documentation

---

## Out of Scope

- Command implementation changes
- New feature development
- Performance optimization
- Multi-agent workflow changes
