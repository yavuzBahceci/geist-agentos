# Task Group 10: Documentation and Integration

**Spec:** Context Enrichment and Global Standards
**Scope:** This prompt covers ONLY Task Group 10. Do NOT implement other task groups.

---

## Task to Implement

**Scope**: Update documentation and ensure integration

**Affected Files**:
- `profiles/default/docs/COMMAND-FLOWS.md`
- `profiles/default/docs/command-references/` (new or update)
- `profiles/default/README.md` (if exists)

### Tasks

10.1. **Update Command Flow Documentation**
- Document new fix-bug command in command flows
- Update spec/implementation command documentation with context enrichment
- Document library basepoints knowledge extraction workflow

10.2. **Create Command Reference Documentation**
- Document fix-bug command usage and options
- Document enhanced orchestrate-tasks iterative execution
- Document implement-task comprehensive knowledge extraction

10.3. **Update Integration Points**
- Ensure all commands reference correct workflows
- Verify workflow references are consistent
- Test command chain integration

---

## Context

Read these files to understand the context:
- @agent-os/specs/2026-01-18-context-enrichment-and-global-standards/spec.md
- @agent-os/specs/2026-01-18-context-enrichment-and-global-standards/planning/requirements.md

---

## Standards to Follow

@agent-os/standards/global/conventions.md
@agent-os/standards/documentation/standards.md

---

## Implementation Instructions

1. Implement ONLY the tasks listed above (Task Group 10)
2. Mark each completed task with `[x]` in `tasks.md`
3. Follow the workflow: Implement all tasks assigned to you and ONLY those task(s) that have been assigned to you.

## Implementation Notes

- This task group should be implemented LAST after all other task groups are complete
- Documentation should reflect all the changes made in Task Groups 1-9
- Ensure command flows document the new fix-bug command
- Update any existing documentation that references the enhanced commands
- Verify all workflow references are consistent across commands
- Keep documentation technology-agnostic as it's in `profiles/default/`
