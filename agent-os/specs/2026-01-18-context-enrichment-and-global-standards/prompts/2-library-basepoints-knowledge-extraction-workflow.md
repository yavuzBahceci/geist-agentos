# Task Group 2: Library Basepoints Knowledge Extraction Workflow

**Spec:** Context Enrichment and Global Standards
**Scope:** This prompt covers ONLY Task Group 2. Do NOT implement other task groups.

---

## Task to Implement

**Scope**: Create workflow for extracting knowledge from library basepoints

**Affected Files**:
- `profiles/default/workflows/common/extract-library-basepoints-knowledge.md` (new)
- `profiles/default/workflows/common/extract-basepoints-with-scope-detection.md` (enhance)

### Tasks

2.1. **Create Library Basepoints Knowledge Extraction Workflow**
- Create new workflow `extract-library-basepoints-knowledge.md`
- Extract knowledge from `agent-os/basepoints/libraries/` folder structure
- Support category-based organization (data, domain, util, infrastructure, framework)
- Extract patterns, workflows, best practices, troubleshooting guidance

2.2. **Integrate Library Basepoints into Scope Detection Workflow**
- Enhance `extract-basepoints-with-scope-detection.md` to include library basepoints
- Add library knowledge to extracted context based on spec scope
- Cache library knowledge alongside regular basepoints knowledge

2.3. **Create Knowledge Accumulation Mechanism**
- Implement mechanism for knowledge to accumulate across commands
- Each command builds upon previous command's enriched context
- Store accumulated knowledge in spec's implementation cache

---

## Context

Read these files to understand the context:
- @agent-os/specs/2026-01-18-context-enrichment-and-global-standards/spec.md
- @agent-os/specs/2026-01-18-context-enrichment-and-global-standards/planning/requirements.md

---

## Standards to Follow

@agent-os/standards/global/conventions.md
@agent-os/standards/global/coding-style.md
@agent-os/standards/global/enriched-knowledge-templates.md

---

## Implementation Instructions

1. Implement ONLY the tasks listed above (Task Group 2)
2. Mark each completed task with `[x]` in `tasks.md`
3. Follow the workflow: Implement all tasks assigned to you and ONLY those task(s) that have been assigned to you.

## Implementation Notes

- This is a foundational task group that other task groups depend on
- The library basepoints extraction workflow will be used by all spec/implementation commands
- Knowledge accumulation mechanism enables each command to build upon previous command's context
- Follow existing workflow patterns in `profiles/default/workflows/`
- Keep workflows technology-agnostic
- Cache all extracted knowledge to `$SPEC_PATH/implementation/cache/`
