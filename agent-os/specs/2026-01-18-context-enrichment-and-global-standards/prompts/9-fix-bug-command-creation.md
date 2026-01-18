# Task Group 9: Fix-Bug Command Creation

**Spec:** Context Enrichment and Global Standards
**Scope:** This prompt covers ONLY Task Group 9. Do NOT implement other task groups.

---

## Task to Implement

**Scope**: Create new command for error/issue analysis and fixing

**Affected Files**:
- `profiles/default/commands/fix-bug/single-agent/fix-bug.md` (new)
- `profiles/default/commands/fix-bug/single-agent/1-analyze-issue.md` (new)
- `profiles/default/commands/fix-bug/single-agent/2-research-libraries.md` (new)
- `profiles/default/commands/fix-bug/single-agent/3-integrate-basepoints.md` (new)
- `profiles/default/commands/fix-bug/single-agent/4-analyze-code.md` (new)
- `profiles/default/commands/fix-bug/single-agent/5-synthesize-knowledge.md` (new)
- `profiles/default/commands/fix-bug/single-agent/6-implement-fix.md` (new)

### Tasks

9.1. **Create fix-bug Command Structure**
- Create command folder structure under `profiles/default/commands/fix-bug/`
- Create main command file `fix-bug.md`
- Implement direct command interface with prompt input

9.2. **Implement Phase 1: Issue Analysis**
- Parse input (bug/feedback)
- Extract details from error logs, codes, descriptions
- Identify affected libraries and modules
- Support multiple input formats

9.3. **Implement Phase 2: Library Research**
- Deep-dive research on related libraries
- Research internal architecture and workflows
- Research known issues and bug patterns
- Understand library component interactions in error scenarios

9.4. **Implement Phase 3: Basepoints Integration**
- Extract relevant basepoints knowledge
- Find basepoints describing error location
- Extract patterns and standards related to error context
- Identify similar issues in basepoints

9.5. **Implement Phase 4: Code Analysis**
- Identify exact file/module locations from error logs
- Deep-dive into relevant code files
- Analyze code patterns and flows in error context
- Trace execution paths leading to error

9.6. **Implement Phase 5: Knowledge Synthesis**
- Combine all knowledge sources into comprehensive analysis
- Create unified view of issue context
- Prepare knowledge for fix implementation

9.7. **Implement Phase 6: Iterative Fix Implementation**
- Use synthesized knowledge to implement initial fix
- Apply library-specific patterns and best practices
- Follow basepoints patterns and standards
- Implement iterative refinement loop:
  - If getting closer: continue iterating
  - If worsening: track counter
  - Stop after 3 worsening results
- Present knowledge summary and request guidance when stop condition met

9.8. **Implement Output Formats**
- Success output: analysis, fixes, implementation notes, iteration history
- Stop condition output: knowledge summary, attempted fixes, current state, guidance request

---

## Context

Read these files to understand the context:
- @agent-os/specs/2026-01-18-context-enrichment-and-global-standards/spec.md
- @agent-os/specs/2026-01-18-context-enrichment-and-global-standards/planning/requirements.md

---

## Standards to Follow

@agent-os/standards/global/conventions.md
@agent-os/standards/global/coding-style.md
@agent-os/standards/global/error-handling.md
@agent-os/standards/global/validation.md

---

## Implementation Instructions

1. Implement ONLY the tasks listed above (Task Group 9)
2. Mark each completed task with `[x]` in `tasks.md`
3. Follow the workflow: Implement all tasks assigned to you and ONLY those task(s) that have been assigned to you.

## Implementation Notes

- This is a new command that doesn't exist yet - create the full structure
- The command accepts bugs OR feedbacks via direct command with prompt input
- The 6-phase structure is critical: analyze → research → integrate → analyze code → synthesize → fix
- Iterative fix implementation continues if getting closer, stops after 3 worsening results
- When stop condition is met, present knowledge summary and request guidance
- Keep the command technology-agnostic as it's in `profiles/default/`
- Follow existing command structure patterns in `profiles/default/commands/`
