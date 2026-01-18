# Requirements: Context Enrichment and Global Standards

## Overview

This spec addresses four related improvements to how standards are managed and how context is enriched during spec/implementation commands:

1. **Global Standards Extraction**: During `deploy-agents`, extract only project-wide standards (not feature/module-specific) to avoid overcrowding standards files.

2. **Context Enrichment for Spec/Implementation Commands**: Each spec/implementation command should narrow its focus while expanding knowledge by extracting relevant patterns and standards for the specific spec being worked on. It can extract and research deeper if it sees necessary. The `implement-task` command should specifically extract knowledge from basepoints, product, and library basepoints to create a comprehensive implementation prompt, decide on the best implementation approach, and start implementing using that enriched prompt (similar to `fix-bug` approach). After implementation completes, it should perform final verification to check for problems, gaps, references, and documentation that need updating.

3. **Tech-Stack Basepoint Creation**: During `create-basepoints` (after `adapt-to-product`), create a tech-stack basepoint with deep-dive information about important libraries and technologies used in the project. This includes understanding internal architecture, inner workflows, and troubleshooting guidance (where bugs typically occur, common issue areas).

4. **Error/Issue Analysis and Fix Command**: Create a new command for analyzing and fixing errors/issues (bugs or feedbacks) via direct command with prompt input. The command extracts knowledge through deep-dive library research, basepoints integration, and code analysis, then iteratively fixes the bug or implements feedback. It continues iterating if getting closer to the solution, but stops after 3 worsening results and presents knowledge summary with guidance request.

---

## Requirement 1: Global Standards Extraction During Deploy-Agents

### Current State

- During `deploy-agents` Phase 8 (`specialize-standards`), standards are extracted from basepoints knowledge
- All standards from basepoints are extracted, including both project-wide and module-specific standards
- This can lead to overcrowded standards files with feature/module-specific rules that don't apply globally

### Desired State

- Extract only **project-wide standards** that apply everywhere
- Focus on main patterns that affect the entire project:
  - Testing strategy
  - Lint rules
  - Naming conventions
  - Error handling patterns
  - SDD (Software Design Document) standards
  - Other cross-cutting concerns

**Extraction Guidelines:**
- **Include**: Main structure and important patterns that are:
  - Main parts of big systems
  - Important and repeating many times across the project
  - Applicable to most cases
- **Exclude**: Feature-specific patterns that are:
  - Relevant only to specific modules/features
  - Irrelevant for most cases
  - Can be accessed from basepoints when needed (keep in basepoints, not standards) 

### Success Criteria

1. Standards files contain only rules that apply globally across all modules/features
2. Feature/module-specific patterns remain in basepoints (not extracted to standards)
3. Standards files are concise and not overcrowded
4. Clear separation between global standards and module-specific patterns

---

## Requirement 2: Context Enrichment for Spec/Implementation Commands

### Current State

- Spec/implementation commands (e.g., `shape-spec`, `write-spec`, `create-tasks`, `orchestrate-tasks`, `implement-task`) currently:
  - Extract basepoints knowledge (if available)
  - But do not actively enrich context with spec-specific knowledge
  - Do not use library basepoints knowledge for context enrichment
  - Do not navigate codebase based on basepoints guidance
  - Do not expand knowledge from multiple sources for the specific spec
  - Do not follow consistent strategy of narrowing focus while expanding knowledge after each command
- `orchestrate-tasks` currently only creates prompts but does not execute them
- Commands are in `profiles/default/` and should be technology-agnostic and clean like other default commands

### Desired State

Each spec/implementation command should follow the same consistent strategy:

1. **Narrow Focus**: Scope to the specific spec/task being worked on
   - Each command narrows its focus to the specific scope (spec, task group, task, feature)

2. **Expand Knowledge**: Extract and expand knowledge from multiple sources for that spec/task:
   - **Basepoints**: Extract relevant patterns matching the spec's domain/scope
   - **Product Knowledge**: Include relevant product docs (mission, roadmap, tech-stack)
   - **Library Basepoints**: Extract knowledge from tech-stack basepoints (library documentation, workflows, patterns, internal architecture, troubleshooting guidance)
     - All spec/implementation commands should use library basepoints knowledge
     - Extract relevant library knowledge based on the spec/task scope
     - Include library workflows, patterns, and best practices relevant to the implementation
   - **Codebase Navigation**: Use basepoints to navigate to relevant code locations
   - **Code Reading**: When needed (especially for implementation/task commands), read actual code files with basepoints guidance on where to find necessary information

3. **Knowledge Expansion After Each Command**: After each command completes, knowledge should be expanded:
   - Previous command's enriched context informs the next command
   - Knowledge accumulates and becomes more focused as commands progress
   - Each command builds upon previous knowledge while narrowing scope

4. **Context Enrichment Output**: Each command should produce enriched context that includes:
   - Spec-scoped basepoints knowledge
   - Relevant product information
   - **Library basepoints knowledge** (for ALL spec/implementation commands)
   - Code references with basepoint navigation guidance
   - Extracted patterns applicable to this spec/task

5. **Implementation-Specific Enhancement** (for `implement-task`):
   - **Knowledge Extraction**: Extract knowledge from all sources (basepoints, product, library basepoints)
   - **Prompt Creation**: Create comprehensive implementation prompt with:
     - All extracted knowledge (basepoints, product, library basepoints)
     - Task/feature requirements
     - Implementation patterns and standards
     - Library-specific patterns and best practices
     - Code examples and references
   - **Implementation Decision**: Decide on best possible implementation approach based on:
     - Available patterns from basepoints
     - Product requirements and constraints
     - Library capabilities and workflows from library basepoints
     - Codebase structure and existing patterns
   - **Implementation Execution**: Start implementing using the enriched prompt
   - **Single Task/Feature Support**: Can focus on implementing a single task or feature with full knowledge context
   - **Final Verification**: After implementation completes, perform comprehensive check:
     - **Problems and Gaps**: Identify any problems or gaps in the implementation
     - **References**: Check for references that need to be updated (imports, dependencies, cross-references)
     - **Documentation**: Identify documentation that needs to be updated (README, API docs, inline comments)
     - **Code Quality**: Check for missing dependencies, broken references, or broken code
     - **Pattern Consistency**: Verify consistency with existing patterns and standards
     - **Completeness**: Ensure implementation is complete and addresses all requirements

### Commands Affected

All spec/implementation commands should follow the same strategy (narrow focus + expand knowledge with library basepoints):

- `shape-spec`: 
  - Narrow focus to specific spec requirements
  - Enrich with basepoints, product knowledge, and **library basepoints** for shaping requirements
  - Extract relevant library patterns and workflows that inform requirements

- `write-spec`: 
  - Narrow focus to spec writing scope
  - Enrich with patterns, standards, code examples, and **library basepoints** for writing spec
  - Include library-specific patterns and best practices in spec documentation

- `create-tasks`: 
  - Narrow focus to task breakdown scope
  - Enrich with implementation patterns, code references, and **library basepoints** for task breakdown
  - Use library workflows and patterns to inform task structure

- `orchestrate-tasks`: 
  - Narrow focus to each task group
  - Enrich with implementation knowledge, code reading guidance, and **library basepoints** for each task group
  - **Enhanced Orchestration Approach**: Should not only create prompts, but also:
    - Create prompts for each task group (as currently done)
    - Include library basepoints knowledge in each task group's prompt
    - Run prompts one by one iteratively
    - Always implement using the specifically created prompt for each task group
    - Never implement without using the created specific prompts
    - Iterate until all task groups are completed

- `implement-task`: 
  - Narrow focus to specific task/feature
  - Enrich with detailed code patterns, file locations, and **library basepoints** for implementation
  - **Enhanced Implementation Approach**: Similar to `fix-bug` command, `implement-task` should:
    - Extract knowledge from basepoints, product docs, and library basepoints
    - Create a comprehensive implementation prompt with all extracted knowledge
    - Decide on best possible implementation approach based on knowledge
    - Start implementing using the enriched prompt
    - Can be used for a single task or feature implementation

### Success Criteria

1. Each command outputs enriched context specific to the spec/task being worked on
2. Each command narrows its focus while expanding knowledge (consistent strategy across all commands)
3. Context includes relevant patterns extracted from basepoints (not all basepoints)
4. **Context includes library basepoints knowledge for ALL spec/implementation commands** (not just `implement-task`)
5. Code references include basepoint-guided navigation (where to find information)
6. Code reading happens when needed (especially for implementation commands)
7. Knowledge is expanded from multiple sources: basepoints, product, library basepoints, codebase
8. Knowledge accumulates and becomes more focused as commands progress (each command builds on previous knowledge)
9. Context is scoped to the spec/task domain (not overly broad)
8. **For `implement-task`**: 
   - Comprehensive implementation prompt created with all extracted knowledge
   - Best implementation approach decided based on available knowledge
   - Implementation starts using enriched prompt
   - Single task/feature can be implemented with full knowledge context
   - **Final Verification**: After implementation completes, check for:
     - Problems and gaps in implementation
     - References that need to be updated
     - Documentation that needs to be updated
     - Missing dependencies or imports
     - Broken references or broken code
     - Inconsistencies with existing patterns

9. **For `orchestrate-tasks`**:
   - Creates prompts for each task group (as currently done)
   - Runs prompts one by one iteratively
   - Always implements using the specifically created prompt for each task group
   - Never implements without using the created specific prompts
   - Iterates until all task groups are completed
   - Each task group implementation uses its own specific prompt

10. **Command Structure and Location**:
    - All commands are in `profiles/default/` (template structure)
    - Commands should respect the command structure in the project
    - Commands should be clean and technology-agnostic like other commands in default mode
    - Commands should follow existing patterns and conventions in `profiles/default/`

---

## Requirement 3: Tech-Stack Basepoint Creation During Create-Basepoints

### Current State

- During `create-basepoints`, basepoints are extracted from the codebase structure and patterns
- Tech-stack information exists in `product/tech-stack.md` but is not deeply researched
- No dedicated basepoint for tech-stack with library-specific deep-dive information
- Important libraries (threading, network, HTTP server, etc.) lack detailed documentation about their usage patterns, best practices, and official guidelines

### Desired State

During `create-basepoints` (after `adapt-to-product`), create a tech-stack basepoint that includes:

1. **Tech-Stack Basepoint File**: Create `agent-os/basepoints/tech-stack.md` or similar
2. **Libraries Folder Structure**: Create a folder structure for used libraries organized by usage categories:
   - **Category-Based Organization**: Group libraries by their usage/domain:
     - `agent-os/basepoints/libraries/data/` - Data access, databases, ORM, persistence
     - `agent-os/basepoints/libraries/domain/` - Domain logic, business rules, core models
     - `agent-os/basepoints/libraries/util/` - Utilities, helpers, common functions
     - `agent-os/basepoints/libraries/infrastructure/` - Networking, HTTP, threading, system-level
     - `agent-os/basepoints/libraries/framework/` - Framework components, UI libraries
     - Other categories as needed (monitoring, logging, caching, etc.)
   - **Nested Folders**: When a category becomes crowded, create nested folders:
     - Example: `agent-os/basepoints/libraries/data/databases/` for database-specific libraries
     - Example: `agent-os/basepoints/libraries/data/orm/` for ORM-specific libraries
     - Example: `agent-os/basepoints/libraries/util/validation/` for validation utilities
   - **Flexible Structure**: Structure adapts based on number of libraries in each category
3. **Library Architecture Mirroring**: Mirror the library architecture with basepoints:
   - **Main Library Basepoint**: Create a main basepoint for each important library in the appropriate category folder (e.g., `agent-os/basepoints/libraries/data/[library-name].md`)
     - Document which parts of the library are relevant to the project
     - Document which parts are NOT used (important to know boundaries)
     - Provide overview of library usage patterns in the project
     - Located in the appropriate category folder based on usage/domain
   - **Solution-Specific Basepoints**: For libraries where different solutions/features are used, create separate basepoints for each in the same category:
     - Example: If using different HTTP server solutions from the same library, create basepoints for each solution in `infrastructure/`
     - Example: If using different threading models, create basepoints for each model in `infrastructure/`
     - Example: If using different ORMs, create basepoints for each in `data/orm/`
     - Each solution basepoint documents its specific usage, patterns, and best practices
     - Same category folder as the main library basepoint
4. **Deep-Dive Documentation**: For important libraries based on their importance:
   - **Core Libraries**: Threading, network, HTTP server, database drivers
   - **Framework Libraries**: Main framework, UI libraries, state management
   - **Infrastructure Libraries**: Logging, monitoring, caching
   - Research via web and official documentation
   - Extract best practices from official documentation
   - Include official guidelines and suggestions if available
   - Document project-specific usage patterns
   - **Deep Technical Research**: Understand internal architecture and workflows
   - **Troubleshooting Research**: Document common issue areas and debugging strategies

**Research Sources:**
- Primary: Official library documentation
- Secondary: Official best practices and guidelines
- Tertiary: Community resources and examples (when official sources insufficient)

**Information to Include:**

**Main Library Basepoint:**
- Library purpose and role in the project
- Which parts/features of the library are relevant to the project
- Which parts/features are NOT used (boundaries and scope)
- Overview of library usage patterns across the project
- Integration patterns with other libraries
- Official best practices for the relevant parts
- **Deep Technical Understanding:**
  - Internal architecture and how the library works internally
  - Inner workflows and execution paths
  - Component interactions and data flow
  - Event handling and lifecycle (if applicable)
- **Troubleshooting and Debugging:**
  - Common issue areas and potential problem locations
  - Where bugs typically occur in the library
  - Known gotchas and edge cases
  - Debugging strategies and tools for this library
  - Error patterns and how to diagnose them

**Solution-Specific Basepoints:**
- Specific solution/feature purpose and role in the project
- Official best practices and recommended patterns for this solution
- Common pitfalls and anti-patterns to avoid
- Project-specific usage examples (extracted from codebase)
- Integration patterns with other solutions/libraries
- Performance considerations
- Security considerations (if applicable)
- **Deep Technical Understanding:**
  - How this specific solution works internally
  - Inner workflows and execution paths for this solution
  - Component interactions specific to this solution
- **Troubleshooting and Debugging:**
  - Solution-specific issue areas and potential problems
  - Where bugs typically occur in this solution
  - Solution-specific debugging strategies
  - Error patterns unique to this solution

**Both:**
- Research via web and official documentation
- Extract best practices from official documentation
- Include official guidelines and suggestions if available

### Importance Classification

Libraries should be classified by importance to determine research depth:

- **Critical**: Core infrastructure (threading, networking, HTTP server) → Deep research required
- **Important**: Framework components, major libraries → Moderate research required
- **Supporting**: Utility libraries, helpers → Basic documentation sufficient

### Success Criteria

1. Tech-stack basepoint created during `create-basepoints`
2. Libraries folder structure created with category-based organization (data, domain, util, infrastructure, framework, etc.)
3. Nested folders created when categories become crowded
4. Libraries are categorized by their usage/domain and placed in appropriate folders
5. Main library basepoint created for each important library in the appropriate category, documenting:
   - Which parts are relevant to the project
   - Which parts are NOT used (boundaries)
   - Overview of usage patterns
6. Solution-specific basepoints created when different solutions from the same library are used (in same category folder)
7. Library architecture is mirrored in basepoint structure (main library + solution-specific basepoints)
8. Important libraries have deep-dive information from official documentation
9. Deep technical understanding documented (internal architecture, inner workflows, component interactions)
10. Troubleshooting and debugging guidance included (common issue areas, bug locations, debugging strategies)
11. Best practices and guidelines extracted from official sources
12. Project-specific usage patterns documented alongside library documentation
13. Information is organized by library importance, category (usage/domain), and solution-specific patterns

---

## Requirement 4: Error/Issue Analysis and Fix Command

### Current State

- No dedicated command for analyzing errors and issues systematically
- Error analysis is manual and ad-hoc, requiring developers to:
  - Manually search basepoints for related patterns
  - Manually research library documentation
  - Manually trace through code to find error locations
  - No systematic approach to combining error information with deep technical knowledge
  - Bug fixing requires separate manual steps after analysis

### Desired State

Create a new command (e.g., `fix-bug` or `resolve-issue`) that:

1. **Accepts Multiple Input Formats via Direct Command with Prompt**:
   - **Bugs**: Written error descriptions, error codes, error logs (stack traces, error messages), bug reports
   - **Feedbacks**: Feature requests, improvement suggestions, enhancement descriptions
   - **Direct Command Interface**: Command accepts prompt input (e.g., `/fix-bug "error message here"` or `/resolve-issue "feedback description here"`)
   - Flexible input parsing to handle natural language descriptions

2. **Extracts Knowledge Through Multiple Sources**:
   - **Deep-Dive Library Research**: Research related libraries and their internal workflows that might be causing the error
     - Identify libraries mentioned in error logs/stack traces
     - Research internal architecture and workflows of these libraries
     - Research known issues and bug patterns in these libraries
     - Understand how library components interact in error scenarios
   - **Basepoints Integration**: Combine error analysis with existing basepoints knowledge
     - Find relevant basepoints that describe the error location
     - Extract patterns and standards related to the error context
     - Identify similar issues or patterns in basepoints
   - **Code/Module Deep-Dive**: Analyze specific files and modules where the error occurs
     - Identify exact file/module locations from error logs
     - Deep-dive into relevant code files
     - Analyze code patterns and flows in error context
     - Trace execution paths that lead to the error

3. **Command Structure**:
   - **Phase 1: Issue Analysis**: Parse input (bug/feedback), extract details, identify affected libraries/modules
   - **Phase 2: Library Research**: Deep-dive research on related libraries and their workflows
   - **Phase 3: Basepoints Integration**: Extract relevant basepoints knowledge
   - **Phase 4: Code Analysis**: Deep-dive into specific files/modules where issue occurs
   - **Phase 5: Knowledge Synthesis**: Combine all knowledge sources into comprehensive analysis
   - **Phase 6: Iterative Fix Implementation**: Start fixing the bug or implementing the feedback using synthesized knowledge, with iterative refinement:
     - **Initial Fix Attempt**: Use extracted knowledge to implement initial fix
       - Apply library-specific patterns and best practices from research
       - Follow basepoints patterns and standards
       - Implement fixes based on code analysis findings
       - Test the fix (run validation/build/test if applicable)
     - **Iterative Refinement Loop**:
       - **If fix doesn't work**: Try alternative approach based on knowledge
       - **If getting closer to solution** (fewer errors, partial success): Continue iterating with refined approach
       - **If facing more errors or same errors**: Track worsening results counter
       - **Stop Condition**: Stop after 3 worsening results (more errors or same errors)
     - **Stop and Request Guidance**: When stop condition met:
       - Present synthesized knowledge in a nutshell (key findings from phases 2-5)
       - Explain what was tried and what happened
       - Ask for guidance from user
       - Do NOT continue iterating after stop condition

4. **Output Format**:
   - **If Fix Succeeds**: 
     - Comprehensive issue analysis document (for bugs or feedback)
     - Library-specific deep-dive information
     - Basepoints context relevant to the issue
     - Code analysis of affected files/modules
     - **Implemented Fixes**: Actual code changes made to resolve the bug or implement feedback
     - Implementation notes explaining how knowledge was applied
     - Iteration history (what was tried and what worked)
   - **If Stop Condition Met** (after 3 worsening results):
     - **Knowledge Summary**: Synthesized knowledge in a nutshell (key findings from all phases)
     - **Attempted Fixes**: Summary of what was tried and iteration history
     - **Current State**: Description of current errors/issues
     - **Guidance Request**: Clear explanation of what's needed from user to proceed

5. **Workflow**: 
   - Knowledge extraction phases (1-5) prepare the context
   - Fix implementation phase (6) immediately uses this knowledge to start fixing
   - **Iterative Approach**: Try fixes, iterate if getting closer, stop after 3 worsening results
   - **Stop and Request Guidance**: After 3 worsening results, present knowledge summary and ask for guidance
   - No separate analysis step required - analysis and fix happen in sequence, with iteration until success or stop condition

### Input Formats Supported

**For Bugs:**
- **Error Logs**: Stack traces, error messages, exception details
- **Error Codes**: Numeric or alphanumeric error codes
- **Written Descriptions**: Natural language error/problem descriptions
- **Bug Reports**: Structured or unstructured bug reports
- **Code Snippets**: Error-producing code snippets

**For Feedbacks:**
- **Feature Requests**: Descriptions of desired features or improvements
- **Enhancement Suggestions**: Ideas for improving existing functionality
- **User Feedback**: User-provided feedback about behavior or features
- **Natural Language Descriptions**: Any feedback in natural language format

### Success Criteria

1. New command created with proper structure (phases and workflow)
2. Command accepts bugs OR feedbacks via direct command with prompt input
3. Command accepts multiple input formats (error logs, codes, descriptions, feedbacks, etc.)
4. Command performs deep-dive research on related libraries and their workflows
5. Command integrates basepoints knowledge relevant to the issue
6. Command performs code analysis on specific files/modules where issue occurs
7. Command synthesizes knowledge from all sources into comprehensive analysis
8. Command starts fixing bugs or implementing feedback immediately after knowledge extraction
9. Fix implementation uses extracted knowledge (library research, basepoints, code analysis)
10. Command iterates on fixes - tries alternatives if fix doesn't work
11. Command continues iterating if getting closer to solution (fewer errors, partial success)
12. Command stops after 3 worsening results (more errors or same errors)
13. Command presents knowledge summary and requests guidance when stop condition met
14. Output includes both analysis AND implemented fixes with explanation (if successful)
15. Output includes knowledge summary and guidance request (if stop condition met)

---

## Implementation Scope

### Out of Scope

- Changes to `profiles/default/` template structure (implementation affects installed `agent-os/` only)
  - **Note**: All commands are in `profiles/default/` and should remain technology-agnostic and clean like other default commands
  - Commands should respect the command structure in the project
  - Commands should follow existing patterns and conventions in `profiles/default/`
- Changes to how standards are referenced in commands (keeping existing `{{standards/*}}` patterns)
- Changes to orchestration.yml standards assignment (task-group-specific standards remain unchanged)

### In Scope

1. **Phase 8 (`specialize-standards`) Enhancement**:
   - Filter extracted standards to only include project-wide patterns
   - Exclude feature/module-specific patterns from standards files
   - Keep module-specific patterns in basepoints only

2. **Context Enrichment Workflow**:
   - Create new workflow or enhance existing workflows for spec-scoped knowledge extraction
   - Integrate context enrichment into ALL spec/implementation commands (shape-spec, write-spec, create-tasks, orchestrate-tasks, implement-task)
   - Add library basepoints knowledge extraction to ALL spec/implementation commands
   - Ensure all commands follow consistent strategy: narrow focus + expand knowledge
   - Add codebase navigation based on basepoints guidance
   - Add code reading capabilities for implementation commands
   - Implement knowledge accumulation across commands (each command builds on previous knowledge)
   - **Enhance `implement-task` command**:
     - Extract knowledge from basepoints, product docs, and library basepoints
     - Create comprehensive implementation prompt with extracted knowledge
     - Implement decision-making logic for best implementation approach
     - Support single task/feature implementation with full knowledge context
     - Add final verification phase to check for problems, gaps, references, documentation updates
   - **Enhance `orchestrate-tasks` command**:
     - Keep prompt creation functionality (as currently done)
     - Add prompt execution functionality to run prompts one by one iteratively
     - Ensure implementation always uses the specifically created prompt for each task group
     - Add iteration logic to continue until all task groups are completed
     - Never allow implementation without using created specific prompts
   - **Command Structure Compliance**:
     - Ensure all commands are in `profiles/default/` and follow existing structure
     - Keep commands clean and technology-agnostic like other default commands
     - Respect command structure patterns in the project

3. **Knowledge Caching**:
   - Cache enriched context per spec in `agent-os/specs/[spec]/implementation/cache/`
   - Make enriched context available to subsequent commands in the spec workflow

4. **Tech-Stack Basepoint Creation**:
   - Enhance `create-basepoints` to create tech-stack basepoint after `adapt-to-product`
   - Create library folder structure organized by usage categories (data, domain, util, infrastructure, framework, etc.)
   - Create nested folders when categories become crowded
   - Categorize libraries by their usage/domain for appropriate folder placement
   - Mirror library architecture with main library basepoints and solution-specific basepoints
   - Detect when different solutions from the same library are used and create separate basepoints
   - Implement web research workflow for gathering official documentation
   - Extract best practices from official sources
   - Research internal architecture and workflows for deep technical understanding
   - Research troubleshooting and debugging guidance (common issues, bug locations)
   - Classify libraries by importance to determine research depth

5. **Error/Issue Analysis and Fix Command**:
   - Create new command structure for error/issue analysis and fixing (e.g., `fix-bug` or `resolve-issue`)
   - Implement direct command interface with prompt input (accepts bugs or feedbacks)
   - Implement multi-format input parsing (error logs, codes, descriptions, feedbacks, etc.)
   - Implement deep-dive library research workflow for issue-related libraries
   - Integrate basepoints knowledge extraction for issue context
   - Implement code/file analysis for issue locations
   - Create knowledge synthesis workflow combining all sources
   - Implement iterative fix/implementation phase that:
     - Uses synthesized knowledge to start fixing bugs or implementing feedback
     - Tries alternative approaches if fix doesn't work
     - Continues iterating if getting closer to solution
     - Tracks worsening results (more errors or same errors)
     - Stops after 3 worsening results
     - Presents knowledge summary and requests guidance when stop condition met
   - Generate implementation output (code changes) along with analysis (if successful)
   - Generate knowledge summary and guidance request (if stop condition met)

---

## Open Questions

1. How should we determine which standards are "project-wide" vs "module-specific" during extraction?
   - Strategy: Check if pattern appears in multiple modules or only in one?
   - Strategy: User-defined categories in basepoints?
   - Strategy: Heuristics based on pattern type (naming, error handling = global; module-specific logic = module-specific)?

2. When should code reading happen vs basepoints-only knowledge?
   - Strategy: Always read code for implementation commands?
   - Strategy: Only read code when basepoints indicate insufficient detail?
   - Strategy: User-configurable threshold?

3. How should context enrichment be triggered?
   - Strategy: Automatic at start of each command?
   - Strategy: Explicit step that can be skipped?
   - Strategy: Cached and reused across commands for same spec?

4. How should library importance be determined for tech-stack basepoint?
   - Strategy: User-defined classification during `adapt-to-product`?
   - Strategy: Automatic detection based on usage frequency?
   - Strategy: Heuristics based on library category (threading/network = critical)?

5. How should web research be conducted for library documentation?
   - Strategy: Automatic web search for official documentation URLs?
   - Strategy: Manual URL specification during setup?
   - Strategy: Combination of detection and manual configuration?

6. How should library architecture mirroring determine solution-specific basepoints?
   - Strategy: Detect different usage patterns of the same library in codebase?
   - Strategy: Analyze imports/usage to identify distinct solutions?
   - Strategy: User-defined classification during `adapt-to-product`?

7. How should libraries be categorized by usage for folder organization?
   - Strategy: Automatic detection based on library name and usage patterns?
   - Strategy: Heuristics based on import paths and usage context?
   - Strategy: User-defined categories during `adapt-to-product` or `create-basepoints`?

8. When should nested folders be created within categories?
   - Strategy: Threshold-based (e.g., >5 libraries in a category)?
   - Strategy: Domain-based grouping (e.g., databases, ORM within data category)?
   - Strategy: User-defined organization preferences?

9. How should the error/issue analysis command identify related libraries from error logs or feedback descriptions?
   - Strategy: Parse stack traces and import statements?
   - Strategy: Extract library names from error messages or feedback context?
   - Strategy: Heuristics based on error patterns or feedback domain?

10. How should issue analysis combine library research with basepoints knowledge?
    - Strategy: Automatic correlation based on library/module names?
    - Strategy: Semantic matching of issue descriptions (bug/feedback) with basepoints content?
    - Strategy: User-guided selection of relevant basepoints?

11. What depth of code analysis should be performed for issue locations?
    - Strategy: Always deep-dive into full file context?
    - Strategy: Focus on specific functions/methods mentioned in issue?
    - Strategy: Configurable depth based on issue complexity?

12. How should fix implementation use synthesized knowledge?
    - Strategy: Automatic code generation based on library patterns and basepoints?
    - Strategy: Guided implementation with step-by-step application of knowledge?
    - Strategy: Human-in-the-loop validation before applying fixes?

13. Should the command differentiate between bug fixes and feedback implementation?
   - Strategy: Same workflow for both, just different implementation goals?
   - Strategy: Different phases or workflows for bugs vs feedbacks?
   - Strategy: Unified approach with context-aware implementation?

14. How should the command determine "getting closer to solution" vs "worsening results"?
   - Strategy: Count errors (fewer errors = closer, more errors = worsening)?
   - Strategy: Analyze error types (related errors = closer, different errors = worsening)?
   - Strategy: Compare error messages (similar = same, different = worsening)?
   - Strategy: Combination of error count, types, and message similarity?

15. How should the knowledge summary be structured when stop condition is met?
   - Strategy: Key findings from each knowledge source (library research, basepoints, code analysis)?
   - Strategy: Most relevant patterns and insights?
   - Strategy: Prioritized list of potential causes and approaches?

16. How should `implement-task` create the implementation prompt from extracted knowledge?
   - Strategy: Template-based prompt with knowledge sections (basepoints, product, library basepoints)?
   - Strategy: Narrative prompt that weaves knowledge into implementation guidance?
   - Strategy: Structured prompt with clear sections for each knowledge source?

17. How should `implement-task` decide on best implementation approach?
   - Strategy: Pattern matching against basepoints patterns?
   - Strategy: Library workflow analysis from library basepoints?
   - Strategy: Product constraint analysis?
   - Strategy: Combination of all knowledge sources?

18. How should the final verification phase work after implementation completes?
   - Strategy: Automated checks (linting, tests, reference validation)?
   - Strategy: Pattern-based gap detection?
   - Strategy: Documentation consistency checks?
   - Strategy: Combination of automated and manual verification?

19. How should `orchestrate-tasks` execute prompts iteratively?
   - Strategy: Sequential execution (one task group at a time)?
   - Strategy: Parallel execution with coordination?
   - Strategy: Queue-based execution with status tracking?

20. How should `orchestrate-tasks` ensure implementation always uses created specific prompts?
   - Strategy: Enforce prompt usage in implementation workflow?
   - Strategy: Validate that implementation references the correct prompt?
   - Strategy: Prevent implementation without prompt reference?

21. How should commands maintain technology-agnostic structure in `profiles/default/`?
   - Strategy: Use abstract patterns and placeholders?
   - Strategy: Follow existing command structure conventions?
   - Strategy: Avoid technology-specific implementations in templates?

22. How should library basepoints knowledge be integrated into all spec/implementation commands?
   - Strategy: Extract relevant library knowledge based on spec/task scope?
   - Strategy: Include library workflows and patterns in all command contexts?
   - Strategy: Filter library basepoints by relevance to current spec/task?

23. How should knowledge accumulation work across commands (narrow focus + expand knowledge)?
   - Strategy: Each command passes enriched context to the next command?
   - Strategy: Commands read previous command's output and build upon it?
   - Strategy: Shared knowledge cache that gets refined after each command?

---

## Related Files

- `profiles/default/commands/deploy-agents/single-agent/8-specialize-standards.md` - Standards extraction
- `profiles/default/commands/shape-spec/single-agent/2-shape-spec.md` - Shape spec command
- `profiles/default/commands/write-spec/single-agent/write-spec.md` - Write spec command
- `profiles/default/commands/create-tasks/single-agent/2-create-tasks-list.md` - Create tasks command
- `profiles/default/commands/orchestrate-tasks/orchestrate-tasks.md` - Orchestrate tasks command
- `profiles/default/commands/implement-task/single-agent/implement-task.md` - Implement task command
- `profiles/default/commands/create-basepoints/single-agent/create-basepoints.md` - Create basepoints command
- `profiles/default/commands/adapt-to-product/single-agent/adapt-to-product.md` - Adapt to product command
- `profiles/default/workflows/basepoints/extract-basepoints-knowledge-automatic.md` - Basepoints extraction workflow
- `profiles/default/workflows/common/extract-basepoints-with-scope-detection.md` - Scope-aware basepoints extraction
- `profiles/default/workflows/research/research-library-documentation.md` - (Potential) Library documentation research workflow
- `profiles/default/commands/fix-bug/` - (New) Error/issue analysis and fix command structure
- `profiles/default/workflows/debugging/analyze-issue-context.md` - (Potential) Issue context analysis workflow
- `profiles/default/workflows/debugging/research-issue-libraries.md` - (Potential) Library research for issue analysis workflow
- `profiles/default/workflows/debugging/implement-fix-from-knowledge.md` - (Potential) Fix implementation workflow using synthesized knowledge