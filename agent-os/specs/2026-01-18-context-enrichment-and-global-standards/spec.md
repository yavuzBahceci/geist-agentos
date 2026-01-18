# Specification: Context Enrichment and Global Standards

## Goal

Enhance Agent OS templates in `profiles/default/` to improve standards management and context enrichment across spec/implementation commands, enabling more focused knowledge extraction, library-aware development, and systematic error resolution. These changes will affect projects when Agent OS is deployed/specialized from `profiles/default/` into their `agent-os/` folders.

## User Stories

- As a developer using Agent OS, I want standards files to contain only project-wide rules so that they remain concise and applicable everywhere
- As a developer working on specs, I want all spec/implementation commands to enrich context with library basepoints knowledge so that I can leverage library patterns and best practices during development
- As a developer using Agent OS, I want orchestrate-tasks to execute prompts iteratively so that task groups are implemented with their specifically created prompts
- As a developer fixing bugs, I want a systematic command that combines library research, basepoints knowledge, and code analysis so that I can resolve issues more effectively

## Specific Requirements

**Tech-Stack Basepoint Creation**
- Create tech-stack basepoint during create-basepoints after adapt-to-product completes
- Organize library basepoints in category-based folder structure (data, domain, util, infrastructure, framework)
- Mirror library architecture with main library basepoints and solution-specific basepoints
- Document library boundaries (which parts are used, which are not)
- Research internal architecture, workflows, and troubleshooting guidance for important libraries
- Classify libraries by importance (critical, important, supporting) to determine research depth

**Context Enrichment for Spec/Implementation Commands**
- Apply consistent strategy across all spec/implementation commands: narrow focus while expanding knowledge
- Extract library basepoints knowledge for ALL commands (shape-spec, write-spec, create-tasks, orchestrate-tasks, implement-task)
- Expand knowledge from multiple sources: basepoints, product docs, library basepoints, codebase
- Accumulate knowledge after each command so subsequent commands build upon previous context
- Enhance implement-task command with comprehensive knowledge extraction and prompt creation:
  - Extract knowledge from all sources (basepoints, product docs, library basepoints)
  - Create comprehensive implementation prompt with all extracted knowledge, task requirements, implementation patterns, and library-specific best practices
  - Decide on best implementation approach based on available patterns, product constraints, library capabilities, and codebase structure
  - Execute implementation using the enriched prompt
  - Support single task/feature implementation with full knowledge context
  - Perform final verification after implementation: check for problems, gaps, references, documentation updates, code quality issues, pattern consistency, and completeness
- Enhance orchestrate-tasks to execute prompts one by one iteratively, always using created specific prompts

**Global Standards Extraction**
- Filter standards extraction during deploy-agents Phase 8 to include only project-wide patterns
- Exclude feature/module-specific patterns from standards files (keep them in basepoints)
- Focus on cross-cutting concerns: testing strategy, lint rules, naming conventions, error handling, SDD standards
- Keep standards files concise and not overcrowded with module-specific rules

**Error/Issue Analysis and Fix Command**
- Create new command (fix-bug or resolve-issue) that accepts bugs or feedbacks via direct prompt input
- Extract knowledge through deep-dive library research, basepoints integration, and code analysis
- Implement 6-phase structure: Issue Analysis, Library Research, Basepoints Integration, Code Analysis, Knowledge Synthesis, Iterative Fix Implementation
- Iterate on fixes: continue if getting closer, stop after 3 worsening results
- Present knowledge summary and request guidance when stop condition is met

**Command Structure Compliance**
- Keep all commands in profiles/default/ and ensure they are technology-agnostic and clean
- Respect existing command structure patterns and conventions in profiles/default/
- Follow existing patterns for command organization and workflow integration

## Visual Design

No visual assets provided.

## Existing Code to Leverage

**Basepoints Extraction Workflow**
- Existing `extract-basepoints-with-scope-detection` workflow can be enhanced for library basepoints extraction
- Current basepoints structure and organization patterns should be maintained

**Standards Specialization (deploy-agents Phase 8)**
- Existing `8-specialize-standards.md` phase can be enhanced with filtering logic
- Current standards extraction mechanisms can be leveraged for filtering

**Orchestrate-Tasks Command**
- Current prompt creation functionality should be preserved and enhanced with execution
- Existing orchestration.yml structure should be maintained

**Spec/Implementation Commands**
- Existing command structures (shape-spec, write-spec, create-tasks) can be enhanced with library basepoints knowledge
- Current basepoints knowledge extraction can be extended to include library basepoints

**Basepoints Knowledge to Leverage (if available)**
- Reusable patterns from basepoints that describe command structure and workflow organization
- Standards and flows from basepoints that apply to spec/implementation workflows
- Testing approaches and strategies from basepoints for validating command enhancements
- Historical decisions from basepoints that inform how commands should be structured

## Out of Scope

- Changes to profiles/default/ template structure (enhancements are made to templates in profiles/default/, and these will affect installed agent-os/ folders when Agent OS is deployed/specialized in projects)
- Changes to how standards are referenced in commands (keeping existing {{standards/*}} patterns)
- Changes to orchestration.yml standards assignment (task-group-specific standards remain unchanged)
- Technology-specific implementations in default profile templates (templates remain technology-agnostic)
- Changes to basepoints structure beyond adding library basepoints folder organization
