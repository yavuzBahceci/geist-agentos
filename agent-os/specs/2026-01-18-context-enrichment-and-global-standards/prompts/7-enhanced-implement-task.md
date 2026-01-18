# Task Group 7: Enhanced implement-task Command

**Spec:** Context Enrichment and Global Standards
**Scope:** This prompt covers ONLY Task Group 7. Do NOT implement other task groups.

---

## Task to Implement

**Scope**: Comprehensive knowledge extraction and implementation for implement-task

**Affected Files**:
- `profiles/default/commands/implement-tasks/single-agent/1-determine-tasks.md`
- `profiles/default/commands/implement-tasks/single-agent/2-implement-tasks.md`
- `profiles/default/commands/implement-tasks/single-agent/3-verify-implementation.md`

### Tasks

7.1. **Add Comprehensive Knowledge Extraction**
- Extract knowledge from basepoints, product docs, and library basepoints
- Narrow focus to specific task/feature being implemented
- Include library-specific patterns and best practices

7.2. **Create Implementation Prompt Generation**
- Create comprehensive implementation prompt with all extracted knowledge
- Include task requirements, implementation patterns, library-specific guidance
- Include code examples and references from basepoints

7.3. **Implement Decision-Making Logic**
- Decide on best implementation approach based on available patterns
- Consider product constraints, library capabilities, codebase structure
- Document decision rationale in implementation output

7.4. **Enhance Final Verification Phase**
- Check for problems and gaps in implementation
- Verify references that need updating (imports, dependencies)
- Identify documentation that needs updating
- Check for code quality issues (missing dependencies, broken references)
- Verify pattern consistency with existing standards
- Ensure implementation completeness

---

## Context

Read these files to understand the context:
- @agent-os/specs/2026-01-18-context-enrichment-and-global-standards/spec.md
- @agent-os/specs/2026-01-18-context-enrichment-and-global-standards/planning/requirements.md

---

## Standards to Follow

@agent-os/standards/global/conventions.md
@agent-os/standards/global/coding-style.md
@agent-os/standards/global/validation.md

---

## Implementation Instructions

1. Implement ONLY the tasks listed above (Task Group 7)
2. Mark each completed task with `[x]` in `tasks.md`
3. Follow the workflow: Implement all tasks assigned to you and ONLY those task(s) that have been assigned to you.

## Implementation Notes

- This task group depends on Task Group 2 (library basepoints extraction workflow)
- implement-task is the final execution command in the spec/implementation chain
- Should create a comprehensive implementation prompt similar to fix-bug approach
- The final verification phase is critical for catching problems before completion
- Keep the command technology-agnostic as it's in `profiles/default/`
- Support single task/feature implementation with full knowledge context
