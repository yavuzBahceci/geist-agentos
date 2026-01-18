# Task Group 1: Global Standards Extraction Enhancement

**Spec:** Context Enrichment and Global Standards
**Scope:** This prompt covers ONLY Task Group 1. Do NOT implement other task groups.

---

## Task to Implement

**Scope**: Enhance `deploy-agents` Phase 8 to filter standards extraction

**Affected Files**:
- `profiles/default/commands/deploy-agents/single-agent/8-specialize-standards.md`

### Tasks

1.1. **Add Standards Classification Logic**
- Add logic to classify extracted standards as "project-wide" vs "module-specific"
- Use heuristics: patterns appearing in multiple modules = project-wide
- Cross-cutting concerns (testing, lint, naming, error handling, SDD) = project-wide
- Single-module patterns = module-specific (keep in basepoints only)

1.2. **Implement Standards Filtering**
- Filter extracted standards to only include project-wide patterns
- Exclude feature/module-specific patterns from standards files
- Ensure module-specific patterns remain accessible in basepoints

1.3. **Update Standards Output Format**
- Keep standards files concise and focused on global rules
- Document why certain patterns are excluded (reference to basepoints)
- Maintain clear separation between global standards and module-specific patterns

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

1. Implement ONLY the tasks listed above (Task Group 1)
2. Mark each completed task with `[x]` in `tasks.md`
3. Follow the workflow: Implement all tasks assigned to you and ONLY those task(s) that have been assigned to you.

## Implementation Notes

- This task group enhances the existing `8-specialize-standards.md` phase in deploy-agents
- The goal is to filter standards extraction to include only project-wide patterns
- Module-specific patterns should remain in basepoints, not be extracted to standards files
- Use heuristics based on pattern frequency across modules and pattern type (cross-cutting vs feature-specific)
- Keep the command technology-agnostic as it's in `profiles/default/`
