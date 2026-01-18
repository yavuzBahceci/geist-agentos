# Requirements: Profiles Default Audit & Knowledge Consolidation

## Executive Summary

This spec addresses two main concerns:
1. **Audit `profiles/default/`** for quality, completeness, and professional presentation
2. **Consolidate duplicated knowledge** between `config/enriched-knowledge/` and `basepoints/libraries/`

---

## Part 1: Duplication Analysis & Recommendation

### Current State: Two Knowledge Systems

#### System 1: `config/enriched-knowledge/` (Created by adapt-to-product)

**When Created:** During `/adapt-to-product` Phase 2 (Web Research)

**Files:**
- `library-research.md` - Best practices for detected libraries
- `stack-best-practices.md` - Architecture patterns
- `security-notes.md` - Security vulnerabilities
- `version-analysis.md` - Dependency version status
- `domain-knowledge.md` - Industry-specific patterns

**Content Type:** Generic web research about libraries (not project-specific)

**Where Used:**
1. `deploy-agents/1-validate-prerequisites.md` - Loads for specialization hints
2. `deploy-agents/8-specialize-standards.md` - Checks for security issues, outdated deps
3. Referenced in documentation

**Key Characteristics:**
- Created ONCE during initial setup
- Generic best practices from web search
- NOT project-specific (doesn't know how YOU use the library)
- Focuses on: best practices, pitfalls, known issues, versions, security

---

#### System 2: `basepoints/libraries/` (Created by create-basepoints)

**When Created:** During `/create-basepoints` Phase 8 (Generate Library Basepoints)

**Files:**
- `data/[library].md` - Data access libraries
- `domain/[library].md` - Domain logic libraries
- `util/[library].md` - Utility libraries
- `infrastructure/[library].md` - Infrastructure libraries
- `framework/[library].md` - Framework libraries

**Content Type:** Project-specific library knowledge

**Where Used:**
1. `extract-library-basepoints-knowledge.md` - Extracts for spec/implementation commands
2. `shape-spec`, `write-spec`, `create-tasks`, `implement-tasks` - Context enrichment
3. `orchestrate-tasks` - Injected into prompts
4. `fix-bug` - Library troubleshooting

**Key Characteristics:**
- Created AFTER basepoints (requires codebase analysis)
- Project-specific (knows how YOU use the library)
- Combines: official docs + YOUR usage patterns + YOUR boundaries
- Focuses on: how we use it, what we use/don't use, our patterns, troubleshooting

---

### Comparison Table

| Aspect | `config/enriched-knowledge/` | `basepoints/libraries/` |
|--------|------------------------------|-------------------------|
| **When Created** | adapt-to-product (early) | create-basepoints (after codebase analysis) |
| **Project-Specific** | ❌ No | ✅ Yes |
| **Knows Your Usage** | ❌ No | ✅ Yes |
| **Has Boundaries** | ❌ No | ✅ Yes (what you use/don't use) |
| **Has Troubleshooting** | ❌ Limited | ✅ Yes |
| **Security Info** | ✅ Yes (CVEs) | ❌ No |
| **Version Info** | ✅ Yes | ❌ No |
| **Best Practices** | ✅ Generic | ✅ Project-specific |
| **Used By** | deploy-agents | spec/implementation commands |

---

### Recommendation: MERGE with Clear Separation

**Don't eliminate `config/enriched-knowledge/`** - it serves a different purpose:

1. **`config/enriched-knowledge/`** = **Pre-basepoints research** (generic, early)
   - Security vulnerabilities (CVEs) - critical for deploy-agents
   - Version analysis - needed before basepoints exist
   - Generic best practices - useful starting point

2. **`basepoints/libraries/`** = **Post-basepoints knowledge** (project-specific, detailed)
   - How YOUR project uses each library
   - YOUR patterns and boundaries
   - Troubleshooting for YOUR context

**Proposed Solution:**

1. **Keep both, but clarify purpose:**
   - `config/enriched-knowledge/` → "Initial Research" (security, versions, generic)
   - `basepoints/libraries/` → "Project-Specific Knowledge" (usage, patterns, boundaries)

2. **Merge security/version info INTO library basepoints:**
   - When generating library basepoints, ALSO include:
     - Security notes from `security-notes.md`
     - Version status from `version-analysis.md`

3. **Update workflows to use library basepoints as primary source:**
   - `deploy-agents` should also check `basepoints/libraries/` if available
   - `fix-bug` should prioritize library basepoints over enriched-knowledge

---

## Part 2: Profiles Default Audit Requirements

### 2.1 Workflow & Command Completeness Audit

**Goal:** Verify all workflows and commands are complete, have correct references, and follow proper order.

#### Checklist:
- [ ] All `{{workflow/...}}` references resolve to existing files
- [ ] All phase numbers are sequential (no gaps)
- [ ] All commands have proper entry points
- [ ] All workflows have proper inputs/outputs documented
- [ ] No orphaned workflows (workflows not called by any command)
- [ ] No circular references

### 2.2 Standards Compliance Audit

**Goal:** Verify all files respect project standards.

#### Checklist:
- [ ] All files follow naming conventions
- [ ] All markdown files have proper structure
- [ ] All code blocks have language tags
- [ ] No placeholder text remaining (`{{PLACEHOLDER}}`, `[TODO]`, etc.)
- [ ] All files have proper headers and sections
- [ ] Documentation is up-to-date with implementation

### 2.3 Professional GitHub Presentation

**Goal:** Make the library look professional and help developers understand value immediately.

#### Requirements:

1. **README.md Improvements:**
   - Clear value proposition in first 3 lines
   - Visual diagram showing what Geist does
   - Quick start (< 5 commands to try it)
   - Feature comparison table
   - Screenshots/GIFs of output

2. **MANIFEST.md Updates:**
   - Complete file listing with descriptions
   - Clear organization explanation
   - Navigation guide

3. **Visual Documentation:**
   - Command flow diagrams (already have WORKFLOW-MAP.md)
   - Architecture diagram
   - Before/After examples
   - Output examples

4. **Developer Experience:**
   - Clear installation instructions
   - Troubleshooting guide
   - FAQ section
   - Contributing guidelines

---

## Part 3: Specific Issues to Address

### 3.1 Knowledge Flow Clarity

**Issue:** It's unclear when `enriched-knowledge` vs `library-basepoints` is used.

**Solution:** 
- Add clear documentation explaining the two systems
- Update WORKFLOW-MAP.md to show when each is used
- Consider adding a "Knowledge Sources" section to each command

### 3.2 Duplicate Research

**Issue:** `research-library.md` (creates enriched-knowledge) and `research-library-documentation.md` (for library basepoints) do similar things.

**Solution:**
- Clarify that `research-library.md` is for INITIAL generic research
- Clarify that `research-library-documentation.md` is for DEEP project-specific research
- Consider merging into one workflow with depth parameter

### 3.3 Missing Integration

**Issue:** Library basepoints don't include security/version info from enriched-knowledge.

**Solution:**
- Update `generate-library-basepoints.md` to also read from `enriched-knowledge/`
- Add security and version sections to library basepoint template

---

## Success Criteria

1. **Knowledge Consolidation:**
   - [ ] Clear documentation of when each knowledge system is used
   - [ ] Library basepoints include security/version info
   - [ ] No confusion about which source to use

2. **Audit Completeness:**
   - [ ] All workflow references validated
   - [ ] All commands have proper structure
   - [ ] No orphaned or missing files

3. **Professional Presentation:**
   - [ ] README has visual diagram
   - [ ] Quick start guide works
   - [ ] Documentation is comprehensive and clear

4. **Standards Compliance:**
   - [ ] All files follow conventions
   - [ ] No placeholder text
   - [ ] Documentation matches implementation

---

## Implementation Order

1. **Phase 1:** Knowledge consolidation (merge security/version into library basepoints)
2. **Phase 2:** Workflow/command audit (validate all references)
3. **Phase 3:** Standards compliance (clean up files)
4. **Phase 4:** Professional presentation (README, visuals)
5. **Phase 5:** Documentation updates (WORKFLOW-MAP, COMMAND-FLOWS)
