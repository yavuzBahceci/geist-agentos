# Geist - Product Knowledge Summary

## Quick Reference

| Attribute | Value |
|-----------|-------|
| **Name** | Geist |
| **Type** | Developer Tool / AI Agent Framework |
| **Language** | Shell (Bash) + Markdown |
| **License** | MIT |
| **Version** | 2.1.1 |

## One-Line Description

Universal Agent OS that makes AI coding assistants project-aware by auto-detecting tech stacks and specializing commands.

## Core Concept

```
Templates (Generic) → Detection → Specialization → Commands (Project-Specific)
```

## Command Chains

### Setup Chain (One-Time)
```
/adapt-to-product → /create-basepoints → /deploy-agents
```

### Development Chain (Per Feature)
```
/shape-spec → /write-spec → /create-tasks → /implement-tasks
```

## Key Directories

| Directory | Purpose |
|-----------|---------|
| `profiles/default/` | Source templates |
| `agent-os/` | Installed templates |
| `agent-os/product/` | Product documentation |
| `agent-os/basepoints/` | Codebase documentation |
| `agent-os/config/` | Project configuration |
| `scripts/` | Installation scripts |

## Unique Features

1. **Project-Agnostic** - Works with any tech stack
2. **Auto-Detection** - Detects tech stack from config files
3. **Basepoints** - Documents codebase patterns per module
4. **Product-Focused Cleanup** - Removes irrelevant content, expands relevant patterns
5. **Web Search Validation** - 2+ source validation for best practices

## Document Consistency Check

| Document | Status | Notes |
|----------|--------|-------|
| mission.md | ✅ Complete | Vision, users, value prop defined |
| roadmap.md | ✅ Complete | Current state, phases, tech debt |
| tech-stack.md | ✅ Complete | Languages, architecture, dependencies |
| project-profile.yml | ✅ Complete | Detection results |

## Cross-References

- Mission mentions "any project type" → Tech stack confirms "shell-based, no runtime deps"
- Roadmap mentions "hierarchical basepoints" → Future enhancement
- Tech stack mentions "template-based" → Mission confirms "command templates"

## Knowledge Gaps

- No automated test suite
- No CI/CD pipeline documentation
- No contribution guidelines
