# Task Group 5: Context Enrichment for create-tasks Command

**Spec:** Context Enrichment and Global Standards
**Scope:** This prompt covers ONLY Task Group 5. Do NOT implement other task groups.

---

## Task to Implement

**Scope**: Add library basepoints knowledge extraction to create-tasks

**Affected Files**:
- `profiles/default/commands/create-tasks/single-agent/2-create-tasks-list.md`

### Tasks

5.1. **Add Library Basepoints Knowledge Extraction**
- Integrate library basepoints extraction workflow
- Use library workflows and patterns to inform task structure
- Extract implementation patterns and code references with library context

5.2. **Implement Knowledge Accumulation from write-spec**
- Load enriched context from previous write-spec execution
- Build upon previous knowledge while narrowing to task breakdown scope
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

1. Implement ONLY the tasks listed above (Task Group 5)
2. Mark each completed task with `[x]` in `tasks.md`
3. Follow the workflow: Implement all tasks assigned to you and ONLY those task(s) that have been assigned to you.

## Implementation Notes

- This task group depends on Task Group 2 (library basepoints extraction workflow) and Task Group 4 (write-spec enrichment)
- create-tasks follows write-spec in the command chain
- Should load and build upon enriched context from write-spec
- Use library workflows to inform how tasks are structured and broken down
- Keep the command technology-agnostic as it's in `profiles/default/`
