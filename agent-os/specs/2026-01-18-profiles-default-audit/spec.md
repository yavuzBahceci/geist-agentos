# Specification: Profiles Default Audit & Knowledge Consolidation

## Overview

This specification defines the comprehensive audit and improvement of `profiles/default/` templates, including knowledge consolidation between `config/enriched-knowledge/` and `basepoints/libraries/`, workflow/command validation, standards compliance, and professional GitHub presentation.

---

## Goals

1. **Knowledge Consolidation**: Clarify and optimize the relationship between `enriched-knowledge/` and `library-basepoints/`
2. **Completeness Audit**: Validate all workflow references, command structures, and file connections
3. **Standards Compliance**: Ensure all files follow conventions and are clean
4. **Professional Presentation**: Improve documentation and visuals for GitHub

---

## Part 1: Knowledge Consolidation

### 1.1 Current Architecture Analysis

The system currently has two knowledge repositories for library information:

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                        CURRENT KNOWLEDGE ARCHITECTURE                            │
└─────────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────┐    ┌─────────────────────────────────────┐
│  config/enriched-knowledge/         │    │  basepoints/libraries/              │
│  ═══════════════════════════════    │    │  ═══════════════════════════════    │
│                                     │    │                                     │
│  Created: adapt-to-product          │    │  Created: create-basepoints         │
│  (EARLY - before codebase analysis) │    │  (LATE - after codebase analysis)   │
│                                     │    │                                     │
│  Content:                           │    │  Content:                           │
│  • library-research.md              │    │  • data/[library].md                │
│  • stack-best-practices.md          │    │  • domain/[library].md              │
│  • security-notes.md                │    │  • util/[library].md                │
│  • version-analysis.md              │    │  • infrastructure/[library].md      │
│  • domain-knowledge.md              │    │  • framework/[library].md           │
│                                     │    │                                     │
│  Purpose:                           │    │  Purpose:                           │
│  • Generic web research             │    │  • Project-specific knowledge       │
│  • Security vulnerabilities (CVEs)  │    │  • How YOU use each library         │
│  • Version status                   │    │  • YOUR patterns and boundaries     │
│  • Best practices (generic)         │    │  • Troubleshooting for YOUR context │
│                                     │    │                                     │
│  Used By:                           │    │  Used By:                           │
│  • deploy-agents (specialization)   │    │  • shape-spec, write-spec           │
│  • Security checks                  │    │  • create-tasks, implement-tasks    │
│                                     │    │  • orchestrate-tasks, fix-bug       │
└─────────────────────────────────────┘    └─────────────────────────────────────┘
```

### 1.2 Decision: Keep Both Systems with Clear Separation

**Rationale**: These systems serve different purposes at different times:

| Aspect | enriched-knowledge | library-basepoints |
|--------|-------------------|-------------------|
| **Timing** | Before codebase analysis | After codebase analysis |
| **Scope** | Generic (any project using this lib) | Specific (YOUR project) |
| **Security** | ✅ CVEs, vulnerabilities | ❌ Not included |
| **Versions** | ✅ Outdated dependency alerts | ❌ Not included |
| **Usage Patterns** | ❌ Generic patterns | ✅ YOUR patterns |
| **Boundaries** | ❌ Not applicable | ✅ What you use/don't use |

**Key Insight**: `enriched-knowledge` is created BEFORE we know how you use libraries. `library-basepoints` is created AFTER analyzing your codebase.

### 1.3 Enhancement: Merge Security/Version Info

**Problem**: Library basepoints don't include security and version information from enriched-knowledge.

**Solution**: Update `generate-library-basepoints.md` workflow to:

1. Read `config/enriched-knowledge/security-notes.md`
2. Read `config/enriched-knowledge/version-analysis.md`
3. Include relevant security/version info in each library basepoint

**New Library Basepoint Template**:

```markdown
# Library Basepoint: [library-name]

## Project Usage
[How YOUR project uses this library]

## Boundaries
[What features you use / don't use]

## Patterns
[YOUR coding patterns with this library]

## Workflows
[Common workflows in YOUR codebase]

## Best Practices
[Project-specific best practices]

## Security Notes                    ◀── NEW SECTION
[From enriched-knowledge/security-notes.md]
- Known CVEs affecting this version
- Security considerations

## Version Status                    ◀── NEW SECTION
[From enriched-knowledge/version-analysis.md]
- Current version in project
- Latest available version
- Upgrade recommendations

## Troubleshooting
[Common issues and solutions for YOUR usage]

## Resources
[Official docs, tutorials, references]
```

### 1.4 Documentation Updates

Add clear documentation explaining when each system is used:

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                        KNOWLEDGE SYSTEM USAGE TIMELINE                           │
└─────────────────────────────────────────────────────────────────────────────────┘

  /adapt-to-product                    /create-basepoints                 /shape-spec
        │                                     │                                │
        ▼                                     ▼                                ▼
  ┌───────────────┐                   ┌───────────────┐                ┌───────────────┐
  │ Web Research  │                   │ Codebase      │                │ Use Library   │
  │ (Generic)     │                   │ Analysis      │                │ Basepoints    │
  └───────┬───────┘                   └───────┬───────┘                └───────────────┘
          │                                   │
          ▼                                   ▼
  ┌───────────────────────┐           ┌───────────────────────┐
  │ enriched-knowledge/   │           │ basepoints/libraries/ │
  │ • Security (CVEs)     │──────────▶│ • Project-specific    │
  │ • Version status      │  MERGE    │ • Usage patterns      │
  │ • Generic practices   │           │ • + Security info     │
  └───────────────────────┘           │ • + Version status    │
                                      └───────────────────────┘
                                               │
                                               ▼
                                      Used by all spec/impl
                                      commands
```

---

## Part 2: Workflow & Command Completeness Audit

### 2.1 Reference Validation

**Scope**: Validate all `{{workflow/...}}` and `{{PHASE ...}}` references resolve to existing files.

**Audit Checklist**:

```
Commands to Audit:
├── adapt-to-product/
│   └── Validate: All workflow references
│   └── Validate: Phase numbering (1-8)
│
├── plan-product/
│   └── Validate: All workflow references
│   └── Validate: Phase numbering
│
├── create-basepoints/
│   └── Validate: All workflow references
│   └── Validate: Phase numbering (1-8)
│   └── Verify: Phase 8 (generate-library-basepoints) exists
│
├── deploy-agents/
│   └── Validate: All workflow references
│   └── Validate: Phase numbering (1-14)
│
├── cleanup-agent-os/
│   └── Validate: All workflow references
│
├── update-basepoints-and-redeploy/
│   └── Validate: All workflow references
│
├── shape-spec/
│   └── Validate: All workflow references
│   └── Verify: extract-basepoints-with-scope-detection
│   └── Verify: extract-library-basepoints-knowledge
│   └── Verify: accumulate-knowledge
│
├── write-spec/
│   └── Validate: All workflow references
│   └── Verify: Knowledge accumulation
│
├── create-tasks/
│   └── Validate: All workflow references
│   └── Verify: Knowledge accumulation
│
├── implement-tasks/
│   └── Validate: All workflow references
│   └── Verify: Comprehensive verification phase
│
├── orchestrate-tasks/
│   └── Validate: All workflow references
│   └── Verify: Library basepoints injection
│
└── fix-bug/
    └── Validate: All 6 phases exist
    └── Validate: All workflow references
```

### 2.2 Workflow Existence Validation

**Scope**: Verify all referenced workflows exist in `workflows/` directory.

**Categories to Check**:

```
workflows/
├── basepoints/
│   ├── extract-basepoints-knowledge-automatic.md     ✓ Verify exists
│   ├── extract-basepoints-knowledge-on-demand.md     ✓ Verify exists
│   └── organize-and-cache-basepoints-knowledge.md    ✓ Verify exists
│
├── codebase-analysis/
│   ├── analyze-codebase.md                           ✓ Verify exists
│   ├── detect-abstraction-layers.md                  ✓ Verify exists
│   ├── generate-headquarter.md                       ✓ Verify exists
│   ├── generate-module-basepoints.md                 ✓ Verify exists
│   ├── generate-parent-basepoints.md                 ✓ Verify exists
│   └── generate-library-basepoints.md                ✓ Verify exists (NEW)
│
├── common/
│   ├── extract-basepoints-with-scope-detection.md    ✓ Verify exists
│   ├── extract-library-basepoints-knowledge.md       ✓ Verify exists (NEW)
│   └── accumulate-knowledge.md                       ✓ Verify exists (NEW)
│
├── detection/
│   ├── detect-project-profile.md                     ✓ Verify exists
│   └── ...
│
├── research/
│   ├── research-orchestrator.md                      ✓ Verify exists
│   ├── research-library.md                           ✓ Verify exists
│   ├── research-library-documentation.md             ✓ Verify exists (NEW)
│   └── ...
│
├── validation/
│   ├── validate-implementation.md                    ✓ Verify exists
│   └── ...
│
└── cleanup/
    ├── product-focused-cleanup.md                    ✓ Verify exists
    └── ...
```

### 2.3 Orphaned File Detection

**Scope**: Find workflows/files not referenced by any command.

**Process**:
1. List all files in `workflows/`
2. Search all commands for references to each workflow
3. Report any workflows with zero references

---

## Part 3: Standards Compliance Audit

### 3.1 Naming Conventions

**Standard**: All files should follow kebab-case naming.

```
✅ Correct:
   extract-basepoints-knowledge.md
   generate-library-basepoints.md
   
❌ Incorrect:
   extractBasepointsKnowledge.md
   Generate_Library_Basepoints.md
```

### 3.2 Markdown Structure

**Standard**: All markdown files should have:

```markdown
# Title (H1 - only one per file)

## Section (H2 for major sections)

### Subsection (H3 for subsections)

- Bullet points for lists
- Code blocks with language tags

```bash
# Example code block with language tag
echo "Hello"
```
```

### 3.3 Placeholder Audit

**Scope**: Find and report any remaining placeholder text.

**Patterns to Search**:
- `{{PLACEHOLDER}}`
- `[TODO]`
- `[TBD]`
- `FIXME`
- `XXX`
- `[INSERT ...]`

### 3.4 Code Block Language Tags

**Standard**: All code blocks must have language tags.

```
✅ Correct:
   ```bash
   echo "Hello"
   ```

❌ Incorrect:
   ```
   echo "Hello"
   ```
```

---

## Part 4: Professional GitHub Presentation

### 4.1 README.md Improvements

**Current Issues**:
- Good content but could be more scannable
- Visual diagrams exist but could be enhanced
- Quick start is present but could be more prominent

**Enhancements**:

1. **Hero Section** (first 5 lines):
```markdown
# Geist

> **Install AI commands that know YOUR codebase.**

Instead of explaining your patterns every prompt, Geist documents them once and injects them automatically.
```

2. **Visual Value Proposition**:
```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│   WITHOUT GEIST                          WITH GEIST                         │
│   ─────────────                          ──────────                         │
│                                                                             │
│   "Add auth. We use React,               "/shape-spec Add auth"             │
│   TypeScript, Zustand..."                                                   │
│                                           AI already knows:                 │
│   Every. Single. Time.                    ✓ Your tech stack                 │
│                                           ✓ Your patterns                   │
│                                           ✓ Your conventions                │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

3. **Quick Start Badge**:
```markdown
[![Setup Time](https://img.shields.io/badge/setup-~20min-green.svg)](#quick-start)
```

4. **Feature Comparison Table**:

| Feature | Without Geist | With Geist |
|---------|--------------|------------|
| Context per prompt | Manual | Automatic |
| Pattern consistency | Varies | Enforced |
| Codebase knowledge | None | Documented |
| Command validation | Manual | Automatic |

### 4.2 MANIFEST.md Updates

**Enhancements**:
- Add file count per directory
- Add purpose description for each major directory
- Add navigation guide

### 4.3 Visual Documentation

**New/Enhanced Diagrams**:

1. **Architecture Diagram** (add to README):
```
┌─────────────────────────────────────────────────────────────────┐
│                        GEIST ARCHITECTURE                        │
└─────────────────────────────────────────────────────────────────┘

  ┌──────────────────────────────────────────────────────────────┐
  │                     profiles/default/                         │
  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐           │
  │  │  commands/  │  │  workflows/ │  │  standards/ │           │
  │  │  (13)       │  │  (40+)      │  │  (10+)      │           │
  │  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘           │
  │         │                │                │                   │
  │         └────────────────┼────────────────┘                   │
  │                          │                                    │
  │                    COMPILATION                                │
  │                          │                                    │
  └──────────────────────────┼────────────────────────────────────┘
                             │
                             ▼
  ┌──────────────────────────────────────────────────────────────┐
  │                   your-project/agent-os/                      │
  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐           │
  │  │  commands/  │  │  basepoints/│  │  product/   │           │
  │  │  (special-  │  │  (YOUR      │  │  (YOUR      │           │
  │  │   ized)     │  │   patterns) │  │   docs)     │           │
  │  └─────────────┘  └─────────────┘  └─────────────┘           │
  └──────────────────────────────────────────────────────────────┘
```

2. **Command Flow Diagram** (already exists in WORKFLOW-MAP.md - link prominently)

3. **Before/After Examples** (add to README):
   - Show actual command output
   - Show generated spec example
   - Show basepoint example

### 4.4 Developer Experience Improvements

1. **Troubleshooting Guide** (new file: `docs/TROUBLESHOOTING.md`):
   - Common errors and solutions
   - FAQ section
   - Debug tips

2. **Contributing Guidelines** (new file: `CONTRIBUTING.md`):
   - How to add new commands
   - How to add new workflows
   - Testing requirements
   - PR process

---

## Part 5: Implementation Details

### 5.1 Files to Create

| File | Purpose |
|------|---------|
| `docs/TROUBLESHOOTING.md` | Common issues and solutions |
| `docs/KNOWLEDGE-SYSTEMS.md` | Explains enriched-knowledge vs library-basepoints |
| `CONTRIBUTING.md` | Contribution guidelines |

### 5.2 Files to Modify

| File | Changes |
|------|---------|
| `workflows/codebase-analysis/generate-library-basepoints.md` | Add security/version sections |
| `README.md` | Enhance hero section, add architecture diagram |
| `MANIFEST.md` | Add navigation guide, file counts |
| `docs/WORKFLOW-MAP.md` | Add knowledge system documentation |
| `docs/COMMAND-FLOWS.md` | Update with knowledge system info |

### 5.3 Validation Scripts

Create validation script to check:
1. All workflow references resolve
2. No orphaned workflows
3. No placeholder text
4. All code blocks have language tags

---

## Success Criteria

### Knowledge Consolidation
- [ ] Library basepoints include security/version sections
- [ ] Clear documentation of when each knowledge system is used
- [ ] WORKFLOW-MAP.md updated with knowledge flow diagram

### Audit Completeness
- [ ] All workflow references validated (0 broken references)
- [ ] All commands have sequential phase numbers
- [ ] No orphaned workflows
- [ ] All new workflows (extract-library-basepoints-knowledge, accumulate-knowledge, generate-library-basepoints) exist

### Standards Compliance
- [ ] All files follow kebab-case naming
- [ ] All code blocks have language tags
- [ ] No placeholder text remaining
- [ ] All markdown files have proper structure

### Professional Presentation
- [ ] README has enhanced hero section
- [ ] Architecture diagram added
- [ ] TROUBLESHOOTING.md created
- [ ] CONTRIBUTING.md created
- [ ] Quick start is prominent and works

---

## Implementation Order

1. **Task Group 1**: Knowledge Consolidation
   - Update generate-library-basepoints.md to include security/version
   - Create KNOWLEDGE-SYSTEMS.md documentation

2. **Task Group 2**: Workflow Reference Audit
   - Validate all {{workflow/...}} references
   - Fix any broken references
   - Report orphaned workflows

3. **Task Group 3**: Standards Compliance
   - Audit naming conventions
   - Fix code block language tags
   - Remove placeholder text

4. **Task Group 4**: Documentation Enhancement
   - Create TROUBLESHOOTING.md
   - Create CONTRIBUTING.md
   - Update MANIFEST.md

5. **Task Group 5**: README Enhancement
   - Enhance hero section
   - Add architecture diagram
   - Add feature comparison table
   - Make quick start more prominent

6. **Task Group 6**: Final Validation
   - Run comprehensive validation
   - Generate audit report
   - Verify all success criteria met

---

## Appendix: Existing Patterns to Follow

### From conventions.md:
- All outputs go to `agent-os/` directory
- Specs go to `agent-os/specs/[date]-[name]/`
- Use kebab-case for file names

### From WORKFLOW-MAP.md:
- Use ASCII diagrams for visual documentation
- Include workflow category organization
- Document inputs/outputs for each workflow

### From COMMAND-FLOWS.md:
- Document phases with clear numbering
- Include flow diagrams for each command
- Document prerequisites and outputs
