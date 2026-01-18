# Task Group 3: Context Enrichment for shape-spec Command

**Spec:** Context Enrichment and Global Standards
**Scope:** This prompt covers ONLY Task Group 3. Do NOT implement other task groups.

---

## Task to Implement

**Scope**: Add library basepoints knowledge extraction to shape-spec

**Affected Files**:
- `profiles/default/commands/shape-spec/single-agent/2-shape-spec.md`

### Tasks

3.1. **Add Library Basepoints Knowledge Extraction**
- Integrate library basepoints extraction workflow
- Extract relevant library patterns and workflows for shaping requirements
- Include library-specific constraints and capabilities in context, or possible solutions they are providing.

3.2. **Implement Narrow Focus + Expand Knowledge Strategy**
- Narrow focus to specific spec requirements
- Expand knowledge from basepoints, product docs, and library basepoints
- Output enriched context for subsequent commands

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

1. Implement ONLY the tasks listed above (Task Group 3)
2. Mark each completed task with `[x]` in `tasks.md`
3. Follow the workflow: Implement all tasks assigned to you and ONLY those task(s) that have been assigned to you.

## Implementation Notes

- This task group depends on Task Group 2 (library basepoints extraction workflow)
- shape-spec is the first command in the spec/implementation chain
- The enriched context output here will be used by subsequent commands (write-spec, create-tasks, etc.)
- Focus on extracting library knowledge that informs requirements shaping
- Keep the command technology-agnostic as it's in `profiles/default/`
