# Task Group 4: Context Enrichment for write-spec Command

**Spec:** Context Enrichment and Global Standards
**Scope:** This prompt covers ONLY Task Group 4. Do NOT implement other task groups.

---

## Task to Implement

**Scope**: Add library basepoints knowledge extraction to write-spec

**Affected Files**:
- `profiles/default/commands/write-spec/single-agent/write-spec.md`

### Tasks

4.1. **Add Library Basepoints Knowledge Extraction**
- Integrate library basepoints extraction workflow
- Extract library-specific patterns and best practices for spec writing
- Include library workflows and patterns in spec documentation

4.2. **Implement Knowledge Accumulation from shape-spec**
- Load enriched context from previous shape-spec execution
- Build upon previous knowledge while narrowing scope
- Output expanded enriched context for subsequent commands

---

## Context

Read these files to understand the context:
- @agent-os/specs/2026-01-18-context-enrichment-and-global-standards/spec.md
- @agent-os/specs/2026-01-18-context-enrichment-and-global-standards/planning/requirements.md

---

## Standards to Follow

@agent-os/standards/global/conventions.md
@agent-os/standards/global/coding-style.md

---

## Implementation Instructions

1. Implement ONLY the tasks listed above (Task Group 4)
2. Mark each completed task with `[x]` in `tasks.md`
3. Follow the workflow: Implement all tasks assigned to you and ONLY those task(s) that have been assigned to you.

## Implementation Notes

- This task group depends on Task Group 2 (library basepoints extraction workflow) and Task Group 3 (shape-spec enrichment)
- write-spec follows shape-spec in the command chain
- Should load and build upon enriched context from shape-spec
- Include library-specific patterns in the spec documentation being written
- Keep the command technology-agnostic as it's in `profiles/default/`
