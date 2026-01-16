# Spec Writing

## Core Responsibilities

1. **Analyze Requirements**: Load and analyze requirements and visual assets thoroughly
2. **Search for Reusable Code**: Find reusable components and patterns in existing codebase
3. **Create Specification**: Write comprehensive specification document

## Workflow

### Step 1: Analyze Requirements and Context

Read and understand all inputs and THINK HARD:
```bash
# Read the requirements document
cat agent-os/specs/[current-spec]/planning/requirements.md

# Check for visual assets
ls -la agent-os/specs/[current-spec]/planning/visuals/ 2>/dev/null | grep -v "^total" | grep -v "^d"
```

Parse and analyze:
- User's feature description and goals
- Requirements gathered by spec-shaper
- Visual mockups or screenshots (if present)
- Any constraints or out-of-scope items mentioned

### Step 2: Load Basepoints Knowledge and Search for Reusable Code

Before creating specifications, load basepoints knowledge and search the codebase for existing patterns and components that can be reused.

```bash
# Determine spec path
SPEC_PATH="agent-os/specs/[current-spec]"

# Load extracted basepoints knowledge if available
if [ -f "$SPEC_PATH/implementation/cache/basepoints-knowledge.json" ]; then
    EXTRACTED_KNOWLEDGE=$(cat "$SPEC_PATH/implementation/cache/basepoints-knowledge.json")
    SCOPE_DETECTION=$(cat "$SPEC_PATH/implementation/cache/scope-detection/semantic-analysis.json" 2>/dev/null || echo "{}")
    KEYWORD_MATCHING=$(cat "$SPEC_PATH/implementation/cache/scope-detection/keyword-matching.json" 2>/dev/null || echo "{}")
fi
```

Based on the feature requirements and basepoints knowledge, identify relevant patterns:

**From Basepoints Knowledge (if available):**
- Reusable patterns extracted from basepoints
- Common modules that match your needs
- Related modules and dependencies from basepoints
- Standards, flows, and strategies relevant to this spec
- Testing approaches and strategies from basepoints
- Historical decisions and pros/cons that inform this feature

**From Codebase Search:**
- Similar features or functionality
- Existing components or modules that match your needs
- Related logic, services, or classes with similar functionality
- Interface patterns that could be extended
- Data structures or schemas that could be reused

Use appropriate search tools and commands for the project's technology stack to find:
- Components or modules that can be reused or extended
- Patterns to follow from similar features
- Naming conventions used in the codebase (from basepoints standards)
- Architecture patterns already established (from basepoints)

Document your findings for use in the specification, prioritizing basepoints knowledge when available.

### Step 3: Check if Deep Reading is Needed

Before creating the specification, check if deep implementation reading is needed:

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
- Inform specification writing with actual implementation patterns
- Suggest reusable code and modules from actual implementation
- Reference similar logic found in implementation
- Include refactoring opportunities in spec

### Step 4: Check for Trade-offs and Create Checkpoints (if needed)

Before creating the specification, check if trade-offs need to be reviewed:

```bash
# Check for multiple valid patterns or conflicts
{{workflows/human-review/review-trade-offs}}
```

If trade-offs are detected, present them to the user and wait for their decision before proceeding.

Also check for big changes or abstraction layer transitions:

```bash
# Check for big changes or layer transitions
{{workflows/human-review/create-checkpoint}}
```

If a checkpoint is needed, present it to the user and wait for their confirmation before proceeding.

### Step 5: Create Core Specification

Write the main specification to `agent-os/specs/[current-spec]/spec.md`.

DO NOT write actual code in the spec.md document. Just describe the requirements clearly and concisely.

Keep it short and include only essential information for each section.

Follow this structure exactly when creating the content of `spec.md`:

```markdown
# Specification: [Feature Name]

## Goal
[1-2 sentences describing the core objective]

## User Stories
- As a [user type], I want to [action] so that [benefit]
- [repeat for up to 2 max additional user stories]

## Specific Requirements

**Specific requirement name**
- [Up to 8 CONCISE sub-bullet points to clarify specific sub-requirements, design or architectual decisions that go into this requirement, or the technical approach to take when implementing this requirement]

[repeat for up to a max of 10 specific requirements]

## Visual Design
[If mockups provided]

**`planning/visuals/[filename]`**
- [up to 8 CONCISE bullets describing specific UI elements found in this visual to address when building]

[repeat for each file in the `planning/visuals` folder]

## Existing Code to Leverage

**Code, component, or existing logic found**
- [up to 5 bullets that describe what this existing code does and how it should be re-used or replicated when building this spec]

[repeat for up to 5 existing code areas]

**Basepoints Knowledge to Leverage (if available)**
- [Reusable patterns from basepoints that should be used]
- [Common modules from basepoints that can be referenced]
- [Standards and flows from basepoints that apply to this spec]
- [Testing approaches from basepoints that should be followed]
- [Historical decisions from basepoints that inform this feature]

## Out of Scope
- [up to 10 concise descriptions of specific features that are out of scope and MUST NOT be built in this spec]
```

## Important Constraints

1. **Always search for reusable code** before specifying new components
2. **Reference visual assets** when available
3. **Do NOT write actual code** in the spec
4. **Keep each section short**, with clear, direct, skimmable specifications
5. **Do NOT deviate from the template above** and do not add additional sections
