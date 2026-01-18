# Tasks: Context Enrichment and Global Standards

## Overview

This tasks list breaks down the spec into implementable task groups. The spec addresses four related improvements:
1. Global Standards Extraction (during deploy-agents)
2. Context Enrichment for Spec/Implementation Commands
3. Tech-Stack Basepoint Creation (during create-basepoints)
4. Error/Issue Analysis and Fix Command

All changes are made to templates in `profiles/default/` which will affect projects when Agent OS is deployed/specialized.

---

## Task Group 1: Global Standards Extraction Enhancement

**Scope**: Enhance `deploy-agents` Phase 8 to filter standards extraction

**Affected Files**:
- `profiles/default/commands/deploy-agents/single-agent/8-specialize-standards.md`

### Tasks

1.1. **Add Standards Classification Logic** [x]
- Add logic to classify extracted standards as "project-wide" vs "module-specific"
- Use heuristics: patterns appearing in multiple modules = project-wide
- Cross-cutting concerns (testing, lint, naming, error handling, SDD) = project-wide
- Single-module patterns = module-specific (keep in basepoints only)

1.2. **Implement Standards Filtering** [x]
- Filter extracted standards to only include project-wide patterns
- Exclude feature/module-specific patterns from standards files
- Ensure module-specific patterns remain accessible in basepoints

1.3. **Update Standards Output Format** [x]
- Keep standards files concise and focused on global rules
- Document why certain patterns are excluded (reference to basepoints)
- Maintain clear separation between global standards and module-specific patterns

---

## Task Group 2: Library Basepoints Knowledge Extraction Workflow

**Scope**: Create workflow for extracting knowledge from library basepoints

**Affected Files**:
- `profiles/default/workflows/common/extract-library-basepoints-knowledge.md` (new)
- `profiles/default/workflows/common/extract-basepoints-with-scope-detection.md` (enhance)

### Tasks

2.1. **Create Library Basepoints Knowledge Extraction Workflow** [x]
- Create new workflow `extract-library-basepoints-knowledge.md`
- Extract knowledge from `agent-os/basepoints/libraries/` folder structure
- Support category-based organization (data, domain, util, infrastructure, framework)
- Extract patterns, workflows, best practices, troubleshooting guidance

2.2. **Integrate Library Basepoints into Scope Detection Workflow** [x]
- Enhance `extract-basepoints-with-scope-detection.md` to include library basepoints
- Add library knowledge to extracted context based on spec scope
- Cache library knowledge alongside regular basepoints knowledge

2.3. **Create Knowledge Accumulation Mechanism** [x]
- Implement mechanism for knowledge to accumulate across commands
- Each command builds upon previous command's enriched context
- Store accumulated knowledge in spec's implementation cache

---

## Task Group 3: Context Enrichment for shape-spec Command

**Scope**: Add library basepoints knowledge extraction to shape-spec

**Affected Files**:
- `profiles/default/commands/shape-spec/single-agent/2-shape-spec.md`

### Tasks

3.1. **Add Library Basepoints Knowledge Extraction** [x]
- Integrate library basepoints extraction workflow
- Extract relevant library patterns and workflows for shaping requirements
- Include library-specific constraints and capabilities in context, or possible solutions they are providing.

3.2. **Implement Narrow Focus + Expand Knowledge Strategy** [x]
- Narrow focus to specific spec requirements
- Expand knowledge from basepoints, product docs, and library basepoints
- Output enriched context for subsequent commands

---

## Task Group 4: Context Enrichment for write-spec Command

**Scope**: Add library basepoints knowledge extraction to write-spec

**Affected Files**:
- `profiles/default/commands/write-spec/single-agent/write-spec.md`

### Tasks

4.1. **Add Library Basepoints Knowledge Extraction** [x]
- Integrate library basepoints extraction workflow
- Extract library-specific patterns and best practices for spec writing
- Include library workflows and patterns in spec documentation

4.2. **Implement Knowledge Accumulation from shape-spec** [x]
- Load enriched context from previous shape-spec execution
- Build upon previous knowledge while narrowing scope
- Output expanded enriched context for subsequent commands

---

## Task Group 5: Context Enrichment for create-tasks Command

**Scope**: Add library basepoints knowledge extraction to create-tasks

**Affected Files**:
- `profiles/default/commands/create-tasks/single-agent/2-create-tasks-list.md`

### Tasks

5.1. **Add Library Basepoints Knowledge Extraction** [x]
- Integrate library basepoints extraction workflow
- Use library workflows and patterns to inform task structure
- Extract implementation patterns and code references with library context

5.2. **Implement Knowledge Accumulation from write-spec** [x]
- Load enriched context from previous write-spec execution
- Build upon previous knowledge while narrowing to task breakdown scope
- Output expanded enriched context for subsequent commands

---

## Task Group 6: Enhanced orchestrate-tasks Command

**Scope**: Add iterative prompt execution to orchestrate-tasks

**Affected Files**:
- `profiles/default/commands/orchestrate-tasks/orchestrate-tasks.md`

### Tasks

6.1. **Add Library Basepoints Knowledge Extraction** [x]
- Integrate library basepoints extraction workflow for each task group
- Include library basepoints knowledge in each task group's prompt
- Extract relevant library knowledge based on task group scope

6.2. **Implement Iterative Prompt Execution** [x]
- Keep existing prompt creation functionality
- Add prompt execution functionality to run prompts one by one
- Implement iteration logic to continue until all task groups completed
- Ensure implementation always uses the specifically created prompt

6.3. **Enforce Prompt Usage** [x]
- Prevent implementation without using created specific prompts
- Validate that implementation references the correct prompt
- Track task group completion status

---

## Task Group 7: Enhanced implement-task Command

**Scope**: Comprehensive knowledge extraction and implementation for implement-task

**Affected Files**:
- `profiles/default/commands/implement-tasks/single-agent/1-determine-tasks.md`
- `profiles/default/commands/implement-tasks/single-agent/2-implement-tasks.md`
- `profiles/default/commands/implement-tasks/single-agent/3-verify-implementation.md`

### Tasks

7.1. **Add Comprehensive Knowledge Extraction** [x]
- Extract knowledge from basepoints, product docs, and library basepoints
- Narrow focus to specific task/feature being implemented
- Include library-specific patterns and best practices

7.2. **Create Implementation Prompt Generation** [x]
- Create comprehensive implementation prompt with all extracted knowledge
- Include task requirements, implementation patterns, library-specific guidance
- Include code examples and references from basepoints

7.3. **Implement Decision-Making Logic** [x]
- Decide on best implementation approach based on available patterns
- Consider product constraints, library capabilities, codebase structure
- Document decision rationale in implementation output

7.4. **Enhance Final Verification Phase** [x]
- Check for problems and gaps in implementation
- Verify references that need updating (imports, dependencies)
- Identify documentation that needs updating
- Check for code quality issues (missing dependencies, broken references)
- Verify pattern consistency with existing standards
- Ensure implementation completeness

---

## Task Group 8: Tech-Stack Basepoint Creation

**Scope**: Create tech-stack basepoint during create-basepoints

**Affected Files**:
- `profiles/default/commands/create-basepoints/single-agent/create-basepoints.md`
- `profiles/default/commands/create-basepoints/single-agent/8-generate-library-basepoints.md` (new phase)
- `profiles/default/workflows/codebase-analysis/generate-library-basepoints.md` (new - main workflow)
- `profiles/default/workflows/research/research-library-documentation.md` (new - research helper)

### Tasks

8.1. **Create Library Documentation Research Workflow** [x]
- Create new workflow `research-library-documentation.md`
- Implement web research for official documentation
- Extract best practices from official sources
- Research internal architecture and workflows
- Research troubleshooting and debugging guidance

8.2. **Create Library Folder Structure** [x]
- Create category-based folder structure under `agent-os/basepoints/libraries/`
- Support categories: data, domain, util, infrastructure, framework
- Create nested folders when categories become crowded
- Categorize libraries by their usage/domain

8.3. **Implement Library Importance Classification** [x]
- Classify libraries by importance: critical, important, supporting
- Determine research depth based on importance
- Critical = deep research, Important = moderate, Supporting = basic

8.4. **Create Main Library Basepoints** [x]
- Create main basepoint for each important library
- Document which parts are relevant to the project
- Document which parts are NOT used (boundaries)
- Include internal architecture and workflows
- Include troubleshooting and debugging guidance

8.5. **Create Solution-Specific Basepoints** [x]
- Detect when different solutions from same library are used
- Create separate basepoints for each solution
- Document solution-specific patterns and best practices
- Include solution-specific troubleshooting guidance

8.6. **Integrate into create-basepoints Command** [x]
- Add new phase after headquarter generation (Phase 8)
- Trigger library basepoint creation after module basepoints
- Ensure tech-stack basepoint is created with library folder structure

8.7. **Create Comprehensive Library Basepoints Generation Workflow** [x]
- Create `workflows/codebase-analysis/generate-library-basepoints.md`
- Combine product knowledge (tech-stack.md) with project basepoints knowledge
- Analyze codebase for actual implementation patterns and usage
- Research official documentation based on library importance
- Generate library basepoints with boundaries (what is/isn't used)
- Create library index

---

## Task Group 9: Fix-Bug Command Creation

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

9.1. **Create fix-bug Command Structure** [x]
- Create command folder structure under `profiles/default/commands/fix-bug/`
- Create main command file `fix-bug.md`
- Implement direct command interface with prompt input

9.2. **Implement Phase 1: Issue Analysis** [x]
- Parse input (bug/feedback)
- Extract details from error logs, codes, descriptions
- Identify affected libraries and modules
- Support multiple input formats

9.3. **Implement Phase 2: Library Research** [x]
- Deep-dive research on related libraries
- Research internal architecture and workflows
- Research known issues and bug patterns
- Understand library component interactions in error scenarios

9.4. **Implement Phase 3: Basepoints Integration** [x]
- Extract relevant basepoints knowledge
- Find basepoints describing error location
- Extract patterns and standards related to error context
- Identify similar issues in basepoints

9.5. **Implement Phase 4: Code Analysis** [x]
- Identify exact file/module locations from error logs
- Deep-dive into relevant code files
- Analyze code patterns and flows in error context
- Trace execution paths leading to error

9.6. **Implement Phase 5: Knowledge Synthesis** [x]
- Combine all knowledge sources into comprehensive analysis
- Create unified view of issue context
- Prepare knowledge for fix implementation

9.7. **Implement Phase 6: Iterative Fix Implementation** [x]
- Use synthesized knowledge to implement initial fix
- Apply library-specific patterns and best practices
- Follow basepoints patterns and standards
- Implement iterative refinement loop:
  - If getting closer: continue iterating
  - If worsening: track counter
  - Stop after 3 worsening results
- Present knowledge summary and request guidance when stop condition met

9.8. **Implement Output Formats** [x]
- Success output: analysis, fixes, implementation notes, iteration history
- Stop condition output: knowledge summary, attempted fixes, current state, guidance request

---

## Task Group 10: Documentation and Integration

**Scope**: Update documentation and ensure integration

**Affected Files**:
- `profiles/default/docs/COMMAND-FLOWS.md`
- `profiles/default/docs/command-references/fix-bug.md` (new)
- `profiles/default/README.md` (if exists)

### Tasks

10.1. **Update Command Flow Documentation** [x]
- Document new fix-bug command in command flows
- Update spec/implementation command documentation with context enrichment
- Document library basepoints knowledge extraction workflow

10.2. **Create Command Reference Documentation** [x]
- Document fix-bug command usage and options
- Document enhanced orchestrate-tasks iterative execution
- Document implement-task comprehensive knowledge extraction

10.3. **Update Integration Points** [x]
- Ensure all commands reference correct workflows
- Verify workflow references are consistent
- Test command chain integration

---

## Implementation Order

**Recommended sequence** (dependencies considered):

1. **Task Group 2** - Library Basepoints Knowledge Extraction Workflow (foundation)
2. **Task Group 8** - Tech-Stack Basepoint Creation (creates library basepoints)
3. **Task Group 1** - Global Standards Extraction Enhancement
4. **Task Group 3** - Context Enrichment for shape-spec
5. **Task Group 4** - Context Enrichment for write-spec
6. **Task Group 5** - Context Enrichment for create-tasks
7. **Task Group 6** - Enhanced orchestrate-tasks
8. **Task Group 7** - Enhanced implement-task
9. **Task Group 9** - Fix-Bug Command Creation
10. **Task Group 10** - Documentation and Integration

---

## Success Criteria Summary

- [x] Standards files contain only project-wide rules (not module-specific)
- [x] All spec/implementation commands extract library basepoints knowledge
- [x] Knowledge accumulates across commands (each builds on previous)
- [x] orchestrate-tasks executes prompts iteratively using created specific prompts
- [x] implement-task creates comprehensive implementation prompt and performs final verification
- [x] Tech-stack basepoint created with library folder structure during create-basepoints
- [x] Library basepoints include deep technical understanding and troubleshooting guidance
- [x] fix-bug command created with 6-phase structure and iterative fix implementation
- [x] All commands remain technology-agnostic and follow existing patterns in profiles/default/
