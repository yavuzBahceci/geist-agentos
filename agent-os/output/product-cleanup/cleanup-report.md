# Product-Focused Cleanup Report

**Generated**: 2026-01-18
**Project**: Geist
**Detected Scope**: Shell-based CLI/Developer Tool

---

## Executive Summary

Geist is a **shell-based developer tool** that uses **markdown templates** and **YAML configuration**. The cleanup analysis identified content that could be simplified (removed) and areas for expansion.

### Scope Detection Results

| Category | Detected Value | Confidence |
|----------|----------------|------------|
| Language | Shell (Bash) | 95% |
| Frameworks | None | 100% |
| Project Type | CLI Tool | 90% |
| Architecture | Template-based | 85% |

---

## Phase A: Simplify (Remove Irrelevant Content)

### Content Categories to Review

Since Geist is a **meta-tool** (Agent OS for Agent OS), most of the "technology-specific" content in templates is **intentionally generic** to support any target project. However, the following could be simplified for Geist's own development:

| Category | Action | Reason |
|----------|--------|--------|
| UI/Frontend workflows | Keep (for target projects) | Geist supports web projects |
| Database patterns | Keep (for target projects) | Geist supports DB projects |
| API patterns | Keep (for target projects) | Geist supports API projects |
| Mobile patterns | Keep (for target projects) | Geist supports mobile projects |

### Recommendation

**No content removal recommended** for Geist itself because:
1. Geist is a template system that must remain technology-agnostic
2. The templates are designed to work with ANY project type
3. Removing content would break Geist's core value proposition

### Files Reviewed (No Changes)

```
agent-os/commands/     - All commands are technology-agnostic ✓
agent-os/workflows/    - All workflows are technology-agnostic ✓
agent-os/standards/    - All standards are technology-agnostic ✓
agent-os/agents/       - All agents are technology-agnostic ✓
```

---

## Phase B: Expand (Enhance with Product-Specific Knowledge)

### Enhancement Opportunities

Since Geist is a developer tool, the following enhancements would be valuable:

| Enhancement | Priority | Description |
|-------------|----------|-------------|
| Shell scripting patterns | High | Add robust error handling to install scripts |
| Template syntax docs | High | Document the `{{}}` template syntax |
| Prompt engineering | Medium | Add AI prompt best practices |
| CLI UX patterns | Medium | Add command-line UX guidelines |

### Recommended Additions

#### 1. Shell Script Robustness
```bash
# Add to scripts/common-functions.sh:
# - set -euo pipefail
# - trap handlers for cleanup
# - Better error messages
```

#### 2. Template Syntax Documentation
```markdown
# Add to docs/:
# - Complete {{}} syntax reference
# - Conditional logic examples
# - Workflow injection patterns
```

#### 3. Prompt Engineering Standards
```markdown
# Add to standards/:
# - AI prompt structure guidelines
# - Context window management
# - Token efficiency patterns
```

---

## Web Search Status

| Query | Status | Validated Sources |
|-------|--------|-------------------|
| Bash best practices | Pending | - |
| CLI design patterns | Pending | - |
| Prompt engineering | Pending | - |

**Note**: Web search for trusted information requires manual execution. See `search-queries.md` for queries to run.

---

## Summary

| Metric | Value |
|--------|-------|
| Files analyzed | ~200 |
| Content removed | 0 (intentionally) |
| Enhancements identified | 4 |
| Web searches pending | 3 categories |

### Key Insight

Geist is a **meta-tool** that must remain technology-agnostic in its templates. The "cleanup" for Geist is different from a typical project—instead of removing irrelevant tech, we should focus on:

1. **Improving the tool itself** (shell scripts, documentation)
2. **Keeping templates generic** (they support any project)
3. **Adding developer tool patterns** (CLI UX, prompt engineering)

---

## Next Steps

1. Run `/create-basepoints` to document Geist's own codebase
2. Consider adding shell scripting standards
3. Document template syntax comprehensively
4. Add prompt engineering best practices to standards
