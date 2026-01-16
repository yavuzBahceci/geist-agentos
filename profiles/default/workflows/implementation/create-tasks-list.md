# Task List Creation

## Core Responsibilities

1. **Analyze spec and requirements**: Read and analyze the spec.md and/or requirements.md to inform the tasks list you will create.
2. **Plan task execution order**: Break the requirements into a list of tasks in an order that takes their dependencies into account.
3. **Group tasks by specialization**: Group tasks that require the same skill or domain specialization together (data layer, interface layer, business logic, etc.)
4. **Create Tasks list**: Create the markdown tasks list broken into groups with sub-tasks.

## Workflow

### Step 1: Analyze Spec & Requirements

Read each of these files (whichever are available) and analyze them to understand the requirements for this feature implementation:
- `agent-os/specs/[this-spec]/spec.md`
- `agent-os/specs/[this-spec]/planning/requirements.md`

```bash
# Determine spec path
SPEC_PATH="agent-os/specs/[this-spec]"

# Load extracted basepoints knowledge if available
if [ -f "$SPEC_PATH/implementation/cache/basepoints-knowledge.json" ]; then
    EXTRACTED_KNOWLEDGE=$(cat "$SPEC_PATH/implementation/cache/basepoints-knowledge.json")
    SCOPE_DETECTION=$(cat "$SPEC_PATH/implementation/cache/scope-detection/semantic-analysis.json" 2>/dev/null || echo "{}")
    KEYWORD_MATCHING=$(cat "$SPEC_PATH/implementation/cache/scope-detection/keyword-matching.json" 2>/dev/null || echo "{}")
fi
```

Use your learnings from spec/requirements AND basepoints knowledge (if available) to inform the tasks list and groupings you will create in the next step.

**From Basepoints Knowledge (if available):**
- Use extracted patterns to inform task breakdown
- Suggest existing patterns and checkpoints from basepoints
- Reference project-specific implementation strategies
- Include relevant testing approaches in task creation


### Step 2: Check if Deep Reading is Needed

Before creating tasks, check if deep implementation reading is needed:

```bash
# Check abstraction layer distance
{{workflows/scope-detection/calculate-abstraction-layer-distance}}

# If deep reading is needed, perform deep reading
{{workflows/deep-reading/read-implementation-deep}}

# Load deep reading results if available
if [ -f "$SPEC_PATH/implementation/cache/deep-reading/implementation-analysis.json" ]; then
    DEEP_READING_RESULTS=$(cat "$SPEC_PATH/implementation/cache/deep-reading/implementation-analysis.json")
    
    # Detect reusable code
    {{workflows/deep-reading/detect-reusable-code}}
fi
```

If deep reading was performed, use the results to:
- Inform task breakdown with actual implementation patterns
- Suggest existing patterns and checkpoints from actual implementation
- Include tasks for reusing existing code
- Include tasks for refactoring opportunities

### Step 2.5: Check for Trade-offs (if needed)

Before creating tasks, check if trade-offs need to be reviewed:

```bash
{{workflows/human-review/review-trade-offs}}
```

If trade-offs are detected, present them to the user and wait for their decision before proceeding.

### Step 3: Check for Checkpoints (if needed)

Before creating tasks, check if checkpoints are needed for big changes:

```bash
{{workflows/human-review/create-checkpoint}}
```

If a checkpoint is needed, present it to the user and wait for their confirmation before proceeding.

### Step 4: Create Tasks Breakdown

Generate `agent-os/specs/[current-spec]/tasks.md`.

**Important**: The exact tasks, task groups, and organization will vary based on the feature's specific requirements. The following is an example format - adapt the content of the tasks list to match what THIS feature actually needs.

```markdown
# Task Breakdown: [Feature Name]

## Overview
Total Tasks: [count]

## Task List

### Core Functionality Layer

#### Task Group 1: Core Logic and Data Structures
**Dependencies:** None

- [ ] 1.0 Complete core functionality layer
  - [ ] 1.1 Write 2-8 focused tests for core functionality
    - Limit to 2-8 highly focused tests maximum
    - Test only critical behaviors (e.g., primary validation, key relationships, core methods)
    - Skip exhaustive coverage of all methods and edge cases
  - [ ] 1.2 Create core data structures or classes
    - Fields/Properties: [list]
    - Validations/Constraints: [list]
    - Reuse pattern from: [existing structure if applicable]
  - [ ] 1.3 Implement data persistence (if applicable)
    - Schema or structure: [description]
    - Relationships: [if applicable]
  - [ ] 1.4 Set up relationships or dependencies
    - [Structure] relates to [related structure]
    - [Structure] depends on [dependency]
  - [ ] 1.5 Ensure core layer tests pass
    - Run ONLY the 2-8 tests written in 1.1
    - Verify data structures work correctly
    - Do NOT run the entire test suite at this stage

**Acceptance Criteria:**
- The 2-8 tests written in 1.1 pass
- Core structures pass validation tests
- Relationships work correctly

### Interface Layer (if applicable)

#### Task Group 2: Interface Implementation
**Dependencies:** Task Group 1

- [ ] 2.0 Complete interface layer
  - [ ] 2.1 Write 2-8 focused tests for interface
    - Limit to 2-8 highly focused tests maximum
    - Test only critical interface behaviors (e.g., primary operations, key error cases)
    - Skip exhaustive testing of all operations and scenarios
  - [ ] 2.2 Create interface implementation
    - Operations: [list of key operations]
    - Follow pattern from: [existing interface]
  - [ ] 2.3 Implement authentication/authorization (if applicable)
    - Use existing auth pattern
    - Add permission checks
  - [ ] 2.4 Add response formatting and error handling
    - Response format: [description]
    - Error handling: [approach]
    - Status indicators: [how success/failure is communicated]
  - [ ] 2.5 Ensure interface layer tests pass
    - Run ONLY the 2-8 tests written in 2.1
    - Verify critical operations work
    - Do NOT run the entire test suite at this stage

**Acceptance Criteria:**
- The 2-8 tests written in 2.1 pass
- All critical operations work
- Proper authorization enforced (if applicable)
- Consistent response format

### User Interface Layer (if applicable)

#### Task Group 3: User Interface Design
**Dependencies:** Task Group 2 (if interface layer exists) or Task Group 1

- [ ] 3.0 Complete user interface
  - [ ] 3.1 Write 2-8 focused tests for UI components
    - Limit to 2-8 highly focused tests maximum
    - Test only critical component behaviors (e.g., primary user interaction, key form submission, main rendering case)
    - Skip exhaustive testing of all component states and interactions
  - [ ] 3.2 Create UI components or views
    - Reuse: [existing component] as base
    - Properties/Props: [list]
    - State: [list]
  - [ ] 3.3 Implement user input handling
    - Fields/Inputs: [list]
    - Validation: [approach]
    - Submit/Process handling
  - [ ] 3.4 Build main view or screen
    - Layout: [description]
    - Components: [list]
    - Match mockup: `planning/visuals/[file]` (if applicable)
  - [ ] 3.5 Apply styling and design
    - Follow existing design system
    - Use design tokens from: [style system]
  - [ ] 3.6 Implement responsive or adaptive design (if applicable)
    - Breakpoints or contexts: [description]
  - [ ] 3.7 Add interactions and feedback
    - User feedback mechanisms
    - Loading states
    - Error states
  - [ ] 3.8 Ensure UI component tests pass
    - Run ONLY the 2-8 tests written in 3.1
    - Verify critical component behaviors work
    - Do NOT run the entire test suite at this stage

**Acceptance Criteria:**
- The 2-8 tests written in 3.1 pass
- Components render correctly
- Forms/inputs validate and process correctly
- Matches visual design (if provided)

### Testing

#### Task Group 4: Test Review & Gap Analysis
**Dependencies:** Task Groups 1-3

- [ ] 4.0 Review existing tests and fill critical gaps only
  - [ ] 4.1 Review tests from Task Groups 1-3
    - Review the 2-8 tests written in Task Group 1
    - Review the 2-8 tests written in Task Group 2 (if applicable)
    - Review the 2-8 tests written in Task Group 3 (if applicable)
    - Total existing tests: approximately 6-24 tests
  - [ ] 4.2 Analyze test coverage gaps for THIS feature only
    - Identify critical user workflows that lack test coverage
    - Focus ONLY on gaps related to this spec's feature requirements
    - Do NOT assess entire application test coverage
    - Prioritize end-to-end workflows over unit test gaps
  - [ ] 4.3 Write up to 10 additional strategic tests maximum
    - Add maximum of 10 new tests to fill identified critical gaps
    - Focus on integration points and end-to-end workflows
    - Do NOT write comprehensive coverage for all scenarios
    - Skip edge cases, performance tests, and accessibility tests unless business-critical
  - [ ] 4.4 Run feature-specific tests only
    - Run ONLY tests related to this spec's feature (tests from 1.1, 2.1, 3.1, and 4.3)
    - Expected total: approximately 16-34 tests maximum
    - Do NOT run the entire application test suite
    - Verify critical workflows pass

**Acceptance Criteria:**
- All feature-specific tests pass (approximately 16-34 tests total)
- Critical user workflows for this feature are covered
- No more than 10 additional tests added when filling in testing gaps
- Testing focused exclusively on this spec's feature requirements

## Execution Order

Recommended implementation sequence:
1. Core Functionality Layer (Task Group 1)
2. Interface Layer (Task Group 2) - if applicable
3. User Interface Layer (Task Group 3) - if applicable
4. Test Review & Gap Analysis (Task Group 4)
```

**Note**: When creating tasks, leverage basepoints knowledge (if available) to:
- Suggest existing patterns and checkpoints from basepoints
- Reference project-specific implementation strategies
- Include relevant testing approaches from basepoints
- Avoid unnecessary work by leveraging existing patterns

**Note**: Adapt this structure based on the actual feature requirements. Some features may need:
- Different task groups (e.g., notification systems, data processing, integration work)
- Different execution order based on dependencies
- More or fewer sub-tasks per group
- Task groups for specific domains (e.g., algorithms, data processing, system integration)

## Important Constraints

- **Create tasks that are specific and verifiable**
- **Group related tasks:** For example, group core logic tasks together, interface tasks together, and UI tasks together (if applicable).
- **Limit test writing during development**:
  - Each task group (1-3) should write 2-8 focused tests maximum
  - Tests should cover only critical behaviors, not exhaustive coverage
  - Test verification should run ONLY the newly written tests, not the entire suite
  - If there is a dedicated test coverage group for filling in gaps in test coverage, this group should add only a maximum of 10 additional tests IF NECESSARY to fill critical gaps
- **Use a focused test-driven approach** where each task group starts with writing 2-8 tests (x.1 sub-task) and ends with running ONLY those tests (final sub-task)
- **Include acceptance criteria** for each task group
- **Reference visual assets** if visuals are available
