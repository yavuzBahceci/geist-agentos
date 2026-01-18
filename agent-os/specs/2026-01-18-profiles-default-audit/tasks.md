# Tasks: Profiles Default Audit & Knowledge Consolidation

## Overview

This document breaks down the specification into implementable task groups. Each task group can be implemented independently but should be done in order for best results.

---

## Task Group 1: Knowledge Consolidation ✅

**Goal**: Clarify and optimize the relationship between `enriched-knowledge/` and `library-basepoints/`

### Tasks

- [x] **1.1** Update `generate-library-basepoints.md` to read security info from `enriched-knowledge/security-notes.md`
  - Add step to check if `config/enriched-knowledge/security-notes.md` exists
  - Extract relevant CVEs and security notes for each library
  - Include in library basepoint under new "Security Notes" section

- [x] **1.2** Update `generate-library-basepoints.md` to read version info from `enriched-knowledge/version-analysis.md`
  - Add step to check if `config/enriched-knowledge/version-analysis.md` exists
  - Extract version status for each library
  - Include in library basepoint under new "Version Status" section

- [x] **1.3** Update library basepoint template to include new sections
  - Add "Security Notes" section after "Best Practices"
  - Add "Version Status" section after "Security Notes"
  - Document what each section should contain

- [x] **1.4** Create `docs/KNOWLEDGE-SYSTEMS.md` documentation
  - Explain the two knowledge systems (enriched-knowledge vs library-basepoints)
  - Document when each is created and by which command
  - Document when each is used and by which commands
  - Include visual timeline diagram
  - Explain the merge strategy

### Acceptance Criteria
- [x] Library basepoints generated after this change include security/version sections
- [x] KNOWLEDGE-SYSTEMS.md exists and clearly explains both systems
- [x] No confusion about which knowledge source to use

---

## Task Group 2: Workflow Reference Audit ✅

**Goal**: Validate all workflow references resolve to existing files

### Tasks

- [x] **2.1** Audit `adapt-to-product` command
  - List all `{{workflow/...}}` references
  - Verify each reference resolves to existing file
  - Verify phase numbering is sequential (1-8)
  - Report any broken references

- [x] **2.2** Audit `plan-product` command
  - List all `{{workflow/...}}` references
  - Verify each reference resolves to existing file
  - Verify phase numbering is sequential
  - Report any broken references

- [x] **2.3** Audit `create-basepoints` command
  - List all `{{workflow/...}}` references
  - Verify each reference resolves to existing file
  - Verify phase numbering is sequential (1-8)
  - Verify Phase 8 (generate-library-basepoints) exists
  - Report any broken references

- [x] **2.4** Audit `deploy-agents` command
  - List all `{{workflow/...}}` references
  - Verify each reference resolves to existing file
  - Verify phase numbering is sequential (1-14)
  - Report any broken references

- [x] **2.5** Audit `shape-spec`, `write-spec`, `create-tasks` commands
  - List all `{{workflow/...}}` references
  - Verify extract-basepoints-with-scope-detection exists
  - Verify extract-library-basepoints-knowledge exists
  - Verify accumulate-knowledge exists
  - Report any broken references

- [x] **2.6** Audit `implement-tasks`, `orchestrate-tasks`, `fix-bug` commands
  - List all `{{workflow/...}}` references
  - Verify each reference resolves to existing file
  - For fix-bug: verify all 6 phases exist
  - Report any broken references

- [x] **2.7** Identify orphaned workflows
  - List all files in `workflows/` directory
  - Search all commands for references to each workflow
  - Report any workflows with zero references

- [x] **2.8** Create audit report
  - Summarize all broken references found
  - Summarize all orphaned workflows found
  - Provide fix recommendations

### Acceptance Criteria
- [x] All workflow references validated
- [x] Audit report generated
- [x] All broken references documented (or fixed)
- [x] All orphaned workflows documented

---

## Task Group 3: Standards Compliance ✅

**Goal**: Ensure all files follow conventions and are clean

### Tasks

- [x] **3.1** Audit naming conventions
  - Scan all files in `profiles/default/`
  - Check for kebab-case compliance
  - Report any files not following convention
  - Fix or document exceptions

- [x] **3.2** Audit code block language tags
  - Scan all markdown files
  - Find code blocks without language tags
  - Add appropriate language tags (bash, markdown, yaml, etc.)
  - Report count of fixes made

- [x] **3.3** Audit placeholder text
  - Search for `{{PLACEHOLDER}}` patterns
  - Search for `[TODO]`, `[TBD]`, `FIXME`, `XXX`
  - Search for `[INSERT ...]` patterns
  - Report all found placeholders
  - Fix or document intentional placeholders

- [x] **3.4** Audit markdown structure
  - Verify each file has exactly one H1 heading
  - Verify proper heading hierarchy (H1 > H2 > H3)
  - Report structural issues
  - Fix critical issues

- [x] **3.5** Create standards compliance report
  - Summarize naming convention issues
  - Summarize code block issues
  - Summarize placeholder issues
  - Summarize structural issues
  - Overall compliance score

### Acceptance Criteria
- [x] All files follow kebab-case naming (or documented exceptions)
- [x] All code blocks have language tags
- [x] No unintentional placeholder text
- [x] All markdown files have proper structure

---

## Task Group 4: Documentation Enhancement ✅

**Goal**: Create missing documentation files

### Tasks

- [x] **4.1** Create `docs/TROUBLESHOOTING.md`
  - Add common errors section
    - "Command not found" errors
    - "File not found" errors
    - "Placeholder not replaced" errors
  - Add FAQ section
    - "How do I update basepoints after code changes?"
    - "How do I add a new command?"
    - "Why is my spec not using my patterns?"
  - Add debug tips section
    - How to check if basepoints are loaded
    - How to verify specialization worked
    - How to trace workflow execution

- [x] **4.2** Create `CONTRIBUTING.md`
  - Add "How to add new commands" section
    - Directory structure
    - Required files
    - Phase numbering
  - Add "How to add new workflows" section
    - Naming conventions
    - Input/output documentation
    - Registration in commands
  - Add "Testing requirements" section
  - Add "PR process" section

- [x] **4.3** Update `MANIFEST.md`
  - Add file counts per directory
  - Add purpose description for each major directory
  - Add navigation guide
  - Update last modified date

- [x] **4.4** Create `docs/KNOWLEDGE-SYSTEMS.md` (replaces WORKFLOW-MAP knowledge section)
  - Explains the two knowledge systems (enriched-knowledge vs library-basepoints)
  - Includes timeline diagram showing when each is created
  - Documents which commands use which knowledge system
  - Explains the merge strategy

- [x] **4.5** Verify `docs/COMMAND-FLOWS.md` completeness
  - All commands are documented (verified in Task Group 2)
  - All phases are documented
  - Knowledge usage documented in KNOWLEDGE-SYSTEMS.md

### Acceptance Criteria
- [x] TROUBLESHOOTING.md exists with useful content
- [x] CONTRIBUTING.md exists with clear guidelines
- [x] MANIFEST.md updated with navigation guide
- [x] KNOWLEDGE-SYSTEMS.md documents knowledge systems
- [x] COMMAND-FLOWS.md is complete and up-to-date

---

## Task Group 5: README Enhancement ✅

**Goal**: Make the library look professional on GitHub

### Tasks

- [x] **5.1** Enhance hero section
  - Make value proposition clearer in first 3 lines
  - Add compelling tagline
  - Ensure badges are up-to-date

- [x] **5.2** Architecture diagram already present
  - README already has comprehensive ASCII diagrams
  - Shows profiles/default structure
  - Shows compilation to your-project/agent-os
  - Shows relationship between commands, workflows, standards

- [x] **5.3** Add feature comparison table
  - Added "Why Geist?" comparison table
  - Compares "Without Geist" vs "With Geist"
  - Includes: context per prompt, pattern consistency, codebase knowledge, validation

- [x] **5.4** Quick start section already prominent
  - README has comprehensive quick start section
  - Commands are copy-pasteable
  - Expected output examples included

- [x] **5.5** Before/after examples already present
  - README has ASCII comparison diagram at top
  - Shows example prompt without Geist vs with Geist

- [x] **5.6** Review and polish overall README
  - Updated hero section with clearer tagline
  - Added "PRs Welcome" badge
  - Added links to new documentation files
  - Last modified date is current (2026-01-18)

### Acceptance Criteria
- [x] Hero section clearly communicates value
- [x] Architecture diagram is clear and accurate
- [x] Feature comparison table is present
- [x] Quick start is prominent and works
- [x] Before/after examples demonstrate value

---

## Task Group 6: Final Validation ✅

**Goal**: Verify all changes and generate final audit report

### Tasks

- [x] **6.1** Run comprehensive workflow reference check
  - Re-run Task Group 2 validation
  - Confirm 0 broken references
  - Confirm 0 critical orphaned workflows

- [x] **6.2** Run comprehensive standards check
  - Re-run Task Group 3 validation
  - Confirm all code blocks have language tags
  - Confirm no placeholder text

- [x] **6.3** Verify documentation completeness
  - Confirm TROUBLESHOOTING.md exists
  - Confirm CONTRIBUTING.md exists
  - Confirm KNOWLEDGE-SYSTEMS.md exists
  - Confirm all docs are linked from README or MANIFEST

- [x] **6.4** Verify knowledge consolidation
  - Test generate-library-basepoints with security/version merge
  - Confirm new sections appear in generated basepoints

- [x] **6.5** Generate final audit report
  - Summary of all changes made
  - List of all new files created
  - List of all files modified
  - Remaining issues (if any)
  - Recommendations for future improvements

- [x] **6.6** Update all "Last Updated" dates
  - README.md
  - profiles/default/README.md
  - All modified documentation files

### Acceptance Criteria
- [x] All workflow references valid
- [x] All standards compliance checks pass
- [x] All documentation files exist and are linked
- [x] Knowledge consolidation working
- [x] Final audit report generated
- [x] All dates updated

---

## Success Criteria Summary

| Category | Criteria | Status |
|----------|----------|--------|
| **Knowledge Consolidation** | Library basepoints include security/version | [x] |
| **Knowledge Consolidation** | KNOWLEDGE-SYSTEMS.md exists | [x] |
| **Audit** | 0 broken workflow references | [x] |
| **Audit** | All commands have sequential phases | [x] |
| **Standards** | All files follow kebab-case | [x] |
| **Standards** | All code blocks have language tags | [x] |
| **Standards** | No placeholder text | [x] |
| **Documentation** | TROUBLESHOOTING.md exists | [x] |
| **Documentation** | CONTRIBUTING.md exists | [x] |
| **Presentation** | README has architecture diagram | [x] |
| **Presentation** | Quick start is prominent | [x] |
| **Validation** | Final audit report generated | [x] |

---

## Implementation Notes

### Order of Execution

1. **Task Group 1** first - establishes knowledge consolidation foundation
2. **Task Group 2** second - identifies issues to fix
3. **Task Group 3** third - cleans up codebase
4. **Task Group 4** fourth - creates missing documentation
5. **Task Group 5** fifth - enhances presentation
6. **Task Group 6** last - validates everything

### Dependencies

- Task Group 4.4 (WORKFLOW-MAP update) depends on Task Group 1 (knowledge consolidation)
- Task Group 5 (README) can reference docs created in Task Group 4
- Task Group 6 must run after all other groups

### Standards to Follow

- Use kebab-case for all new files
- Include language tags on all code blocks
- Use ASCII diagrams for GitHub compatibility
- Follow existing documentation patterns from COMMAND-FLOWS.md and WORKFLOW-MAP.md
