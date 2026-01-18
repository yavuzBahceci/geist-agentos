# Task Group 8: Tech-Stack Basepoint Creation

**Spec:** Context Enrichment and Global Standards
**Scope:** This prompt covers ONLY Task Group 8. Do NOT implement other task groups.

---

## Task to Implement

**Scope**: Create tech-stack basepoint during create-basepoints

**Affected Files**:
- `profiles/default/commands/create-basepoints/single-agent/create-basepoints.md`
- `profiles/default/commands/create-basepoints/single-agent/5-generate-module-basepoints.md` (or new phase)
- `profiles/default/workflows/research/research-library-documentation.md` (new)

### Tasks

8.1. **Create Library Documentation Research Workflow**
- Create new workflow `research-library-documentation.md`
- Implement web research for official documentation
- Extract best practices from official sources
- Research internal architecture and workflows
- Research troubleshooting and debugging guidance

8.2. **Create Library Folder Structure**
- Create category-based folder structure under `agent-os/basepoints/libraries/`
- Support categories: data, domain, util, infrastructure, framework
- Create nested folders when categories become crowded
- Categorize libraries by their usage/domain

8.3. **Implement Library Importance Classification**
- Classify libraries by importance: critical, important, supporting
- Determine research depth based on importance
- Critical = deep research, Important = moderate, Supporting = basic

8.4. **Create Main Library Basepoints**
- Create main basepoint for each important library
- Document which parts are relevant to the project
- Document which parts are NOT used (boundaries)
- Include internal architecture and workflows
- Include troubleshooting and debugging guidance

8.5. **Create Solution-Specific Basepoints**
- Detect when different solutions from same library are used
- Create separate basepoints for each solution
- Document solution-specific patterns and best practices
- Include solution-specific troubleshooting guidance

8.6. **Integrate into create-basepoints Command**
- Add new phase after adapt-to-product completes
- Trigger library basepoint creation after module basepoints
- Ensure tech-stack basepoint is created with library folder structure

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
@agent-os/standards/global/tech-stack.md

---

## Implementation Instructions

1. Implement ONLY the tasks listed above (Task Group 8)
2. Mark each completed task with `[x]` in `tasks.md`
3. Follow the workflow: Implement all tasks assigned to you and ONLY those task(s) that have been assigned to you.

## Implementation Notes

- This task group creates the library basepoints that Task Group 2's workflow will extract from
- Library basepoints are organized by category (data, domain, util, infrastructure, framework)
- Each library basepoint should document both what IS used and what is NOT used (boundaries)
- Deep technical understanding includes internal architecture, workflows, and troubleshooting
- Research depth is determined by library importance classification
- Keep the command technology-agnostic as it's in `profiles/default/`
- Web research should prioritize official documentation over community resources
