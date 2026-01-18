# Task Group 6: Enhanced orchestrate-tasks Command

**Spec:** Context Enrichment and Global Standards
**Scope:** This prompt covers ONLY Task Group 6. Do NOT implement other task groups.

---

## Task to Implement

**Scope**: Add iterative prompt execution to orchestrate-tasks

**Affected Files**:
- `profiles/default/commands/orchestrate-tasks/orchestrate-tasks.md`

### Tasks

6.1. **Add Library Basepoints Knowledge Extraction**
- Integrate library basepoints extraction workflow for each task group
- Include library basepoints knowledge in each task group's prompt
- Extract relevant library knowledge based on task group scope

6.2. **Implement Iterative Prompt Execution**
- Keep existing prompt creation functionality
- Add prompt execution functionality to run prompts one by one
- Implement iteration logic to continue until all task groups completed
- Ensure implementation always uses the specifically created prompt

6.3. **Enforce Prompt Usage**
- Prevent implementation without using created specific prompts
- Validate that implementation references the correct prompt
- Track task group completion status

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

1. Implement ONLY the tasks listed above (Task Group 6)
2. Mark each completed task with `[x]` in `tasks.md`
3. Follow the workflow: Implement all tasks assigned to you and ONLY those task(s) that have been assigned to you.

## Implementation Notes

- This task group depends on Task Group 2 (library basepoints extraction workflow)
- orchestrate-tasks currently only creates prompts but does NOT execute them
- The enhancement adds iterative prompt execution functionality
- Each task group's prompt should include library basepoints knowledge relevant to that task group
- Implementation must always use the specifically created prompt - never implement without it
- Keep the command technology-agnostic as it's in `profiles/default/`
