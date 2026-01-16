Now that knowledge has been merged and conflicts resolved, proceed with specializing the shape-spec and write-spec commands by following these instructions:

## Core Responsibilities

1. **Read Abstract Command Templates**: Load shape-spec and write-spec templates from profiles/default
2. **Inject Project-Specific Knowledge**: Inject patterns, standards, flows, strategies, and testing approaches from merged knowledge
3. **Replace Abstract Placeholders**: Replace {{workflows/...}}, {{standards/...}} with project-specific content
4. **Replace Generic Examples**: Replace generic examples with project-specific patterns from basepoints
5. **Write Specialized Commands**: Write specialized commands to agent-os/commands/ (replace abstract versions)

## Workflow

### Step 1: Load Merged Knowledge

Load merged knowledge from previous phase:

```bash
# Load merged knowledge from cache
if [ -f "agent-os/output/deploy-agents/knowledge/merged-knowledge.json" ]; then
    MERGED_KNOWLEDGE=$(cat agent-os/output/deploy-agents/knowledge/merged-knowledge.json)
    echo "✅ Loaded merged knowledge"
fi

# Load command-specific knowledge
if [ -f "agent-os/output/deploy-agents/knowledge/shape-spec-knowledge.json" ]; then
    SHAPE_SPEC_KNOWLEDGE=$(cat agent-os/output/deploy-agents/knowledge/shape-spec-knowledge.json)
fi

if [ -f "agent-os/output/deploy-agents/knowledge/write-spec-knowledge.json" ]; then
    WRITE_SPEC_KNOWLEDGE=$(cat agent-os/output/deploy-agents/knowledge/write-spec-knowledge.json)
fi
```

### Step 2: Read Abstract shape-spec Command Template

Read abstract shape-spec command template and all its phase files:

```bash
# Read main shape-spec command
if [ -f "profiles/default/commands/shape-spec/shape-spec.md" ]; then
    SHAPE_SPEC_MAIN=$(cat profiles/default/commands/shape-spec/shape-spec.md)
fi

# Read single-agent version
if [ -f "profiles/default/commands/shape-spec/single-agent/shape-spec.md" ]; then
    SHAPE_SPEC_SINGLE=$(cat profiles/default/commands/shape-spec/single-agent/shape-spec.md)
fi

# Read all phase files
SHAPE_SPEC_PHASES=""
for phase_file in profiles/default/commands/shape-spec/single-agent/*.md; do
    if [ -f "$phase_file" ] && [[ "$(basename "$phase_file")" =~ ^[0-9]+- ]]; then
        PHASE_NAME=$(basename "$phase_file")
        PHASE_CONTENT=$(cat "$phase_file")
        SHAPE_SPEC_PHASES="${SHAPE_SPEC_PHASES}\n\n=== $PHASE_NAME ===\n$PHASE_CONTENT"
    fi
done

echo "✅ Loaded abstract shape-spec command template and phase files"
```

### Step 3: Specialize shape-spec Command

Inject project-specific knowledge into shape-spec command:

```bash
# Start with abstract template
SPECIALIZED_SHAPE_SPEC="$SHAPE_SPEC_SINGLE"

# Inject project-specific patterns from basepoints
# Replace abstract patterns with project-specific patterns from merged knowledge
# Example: Replace generic "Repository pattern" with project-specific pattern name from basepoints
SPECIALIZED_SHAPE_SPEC=$(echo "$SPECIALIZED_SHAPE_SPEC" | \
    sed "s|generic pattern|$(extract_pattern_from_merged "$MERGED_KNOWLEDGE" "shape-spec")|g")

# Inject project-specific standards into shape-spec command
# Replace {{standards/global/*}} with project-specific standards references
# Replace with actual project-specific standards from basepoints
SPECIALIZED_SHAPE_SPEC=$(echo "$SPECIALIZED_SHAPE_SPEC" | \
    sed "s|{{standards/global/\*}}|$(generate_project_standards_refs "$MERGED_KNOWLEDGE")|g")

# Replace abstract placeholders ({{workflows/...}}) with project-specific content
# Example: {{workflows/specification/research-spec}} -> project-specific research workflow
SPECIALIZED_SHAPE_SPEC=$(echo "$SPECIALIZED_SHAPE_SPEC" | \
    sed "s|{{workflows/specification/research-spec}}|$(generate_project_workflow_ref "$MERGED_KNOWLEDGE" "research-spec")|g")

# Replace generic examples with project-specific patterns from basepoints
# Example: Replace "Example: User model" with actual project model names from basepoints
SPECIALIZED_SHAPE_SPEC=$(echo "$SPECIALIZED_SHAPE_SPEC" | \
    sed "s|Example: [A-Z][a-z]+ model|Example: $(extract_example_from_basepoints "$MERGED_KNOWLEDGE")|g")

# Inject project-specific patterns into shape-spec phases
for phase_file in profiles/default/commands/shape-spec/single-agent/*.md; do
    if [ -f "$phase_file" ] && [[ "$(basename "$phase_file")" =~ ^[0-9]+- ]]; then
        PHASE_CONTENT=$(cat "$phase_file")
        
        # Inject project-specific patterns
        PHASE_CONTENT=$(inject_patterns_into_phase "$PHASE_CONTENT" "$MERGED_KNOWLEDGE")
        
        # Inject project-specific standards
        PHASE_CONTENT=$(inject_standards_into_phase "$PHASE_CONTENT" "$MERGED_KNOWLEDGE")
        
        # Replace abstract placeholders
        PHASE_CONTENT=$(replace_placeholders_in_phase "$PHASE_CONTENT" "$MERGED_KNOWLEDGE")
        
        # Replace generic examples
        PHASE_CONTENT=$(replace_examples_in_phase "$PHASE_CONTENT" "$MERGED_KNOWLEDGE")
        
        # Specialize basepoints knowledge extraction workflows
        PHASE_CONTENT=$(specialize_basepoints_extraction_workflows "$PHASE_CONTENT" "$MERGED_KNOWLEDGE")
        
        # Specialize scope detection workflows
        PHASE_CONTENT=$(specialize_scope_detection_workflows "$PHASE_CONTENT" "$MERGED_KNOWLEDGE")
        
        # Store specialized phase content
        SPECIALIZED_SHAPE_SPEC_PHASES="${SPECIALIZED_SHAPE_SPEC_PHASES}\n\n=== $(basename "$phase_file") ===\n$PHASE_CONTENT"
    fi
done
```

Specialize shape-spec command:
- **Inject Project-Specific Patterns**: Replace abstract patterns with project-specific patterns from basepoints
  - Extract patterns relevant to specification shaping from merged knowledge
  - Inject patterns into shape-spec phases (1-initialize-spec.md, 2-shape-spec.md)
  - Use project-specific pattern names and structures from basepoints

- **Inject Project-Specific Standards**: Replace abstract standards references with project-specific standards
  - Replace `{{standards/global/*}}` with actual project-specific standards from basepoints
  - Reference project-specific naming conventions, coding styles, structure standards
  - Include project-specific standards in phase files

- **Replace Abstract Placeholders**: Replace workflow and standards placeholders with project-specific content
  - Replace `{{workflows/specification/research-spec}}` with project-specific research workflow references
  - Replace `{{workflows/specification/initialize-spec}}` with project-specific initialization workflow references
  - Replace `{{standards/...}}` with actual project-specific standards content

- **Replace Generic Examples**: Replace generic examples with project-specific patterns from basepoints
  - Replace generic model names with actual project model names from basepoints
  - Replace generic component names with actual project component names
  - Use project-specific patterns and structures as examples

### Step 4: Read Abstract write-spec Command Template

Read abstract write-spec command template and referenced workflows:

```bash
# Read single-agent version
if [ -f "profiles/default/commands/write-spec/single-agent/write-spec.md" ]; then
    WRITE_SPEC_SINGLE=$(cat profiles/default/commands/write-spec/single-agent/write-spec.md)
fi

# Read workflows referenced by write-spec
WRITE_SPEC_WORKFLOWS=""
if [ -f "profiles/default/workflows/specification/write-spec.md" ]; then
    WRITE_SPEC_WORKFLOW_CONTENT=$(cat profiles/default/workflows/specification/write-spec.md)
    WRITE_SPEC_WORKFLOWS="${WRITE_SPEC_WORKFLOWS}\n\n=== write-spec workflow ===\n$WRITE_SPEC_WORKFLOW_CONTENT"
fi

echo "✅ Loaded abstract write-spec command template and workflows"
```

### Step 5: Specialize write-spec Command

Inject project-specific knowledge into write-spec command:

```bash
# Start with abstract template
SPECIALIZED_WRITE_SPEC="$WRITE_SPEC_SINGLE"

# Inject project-specific structure patterns from basepoints
# Replace abstract structure patterns with project-specific patterns from merged knowledge
# Example: Replace generic "component structure" with project-specific component structure from basepoints
SPECIALIZED_WRITE_SPEC=$(echo "$SPECIALIZED_WRITE_SPEC" | \
    sed "s|generic structure|$(extract_structure_from_merged "$MERGED_KNOWLEDGE" "write-spec")|g")

# Inject project-specific testing approaches from basepoints
# Replace abstract testing approaches with project-specific testing approaches
# Example: Replace generic "unit tests" with project-specific test patterns from basepoints
SPECIALIZED_WRITE_SPEC=$(echo "$SPECIALIZED_WRITE_SPEC" | \
    sed "s|generic testing|$(extract_testing_from_merged "$MERGED_KNOWLEDGE" "write-spec")|g")

# Replace abstract placeholders with project-specific content
# Example: {{workflows/specification/write-spec}} -> project-specific write-spec workflow
SPECIALIZED_WRITE_SPEC=$(echo "$SPECIALIZED_WRITE_SPEC" | \
    sed "s|{{workflows/specification/write-spec}}|$(generate_project_workflow_content "$MERGED_KNOWLEDGE" "write-spec")|g")

# Replace {{standards/...}} with project-specific standards
SPECIALIZED_WRITE_SPEC=$(echo "$SPECIALIZED_WRITE_SPEC" | \
    sed "s|{{standards/\*}}|$(generate_project_standards_content "$MERGED_KNOWLEDGE")|g")

# Reference project-specific test strategies from basepoints
# Add project-specific test strategy references based on basepoints knowledge
TEST_STRATEGIES=$(extract_test_strategies_from_basepoints "$MERGED_KNOWLEDGE")
SPECIALIZED_WRITE_SPEC="${SPECIALIZED_WRITE_SPEC}\n\n## Project-Specific Test Strategies\n${TEST_STRATEGIES}"

# Specialize the write-spec workflow itself
SPECIALIZED_WRITE_SPEC_WORKFLOW="$WRITE_SPEC_WORKFLOW_CONTENT"

# Inject project-specific structure patterns into workflow
SPECIALIZED_WRITE_SPEC_WORKFLOW=$(inject_structure_into_workflow "$SPECIALIZED_WRITE_SPEC_WORKFLOW" "$MERGED_KNOWLEDGE")

# Inject project-specific testing approaches into workflow
SPECIALIZED_WRITE_SPEC_WORKFLOW=$(inject_testing_into_workflow "$SPECIALIZED_WRITE_SPEC_WORKFLOW" "$MERGED_KNOWLEDGE")

# Replace abstract placeholders in workflow
SPECIALIZED_WRITE_SPEC_WORKFLOW=$(replace_placeholders_in_workflow "$SPECIALIZED_WRITE_SPEC_WORKFLOW" "$MERGED_KNOWLEDGE")

# Specialize basepoints knowledge extraction workflows in write-spec workflow
SPECIALIZED_WRITE_SPEC_WORKFLOW=$(specialize_basepoints_extraction_workflows "$SPECIALIZED_WRITE_SPEC_WORKFLOW" "$MERGED_KNOWLEDGE")

# Specialize scope detection workflows in write-spec workflow
SPECIALIZED_WRITE_SPEC_WORKFLOW=$(specialize_scope_detection_workflows "$SPECIALIZED_WRITE_SPEC_WORKFLOW" "$MERGED_KNOWLEDGE")

# Specialize deep reading workflows in write-spec workflow
SPECIALIZED_WRITE_SPEC_WORKFLOW=$(specialize_deep_reading_workflows "$SPECIALIZED_WRITE_SPEC_WORKFLOW" "$MERGED_KNOWLEDGE")

Specialize write-spec command:
- **Inject Project-Specific Structure Patterns**: Replace abstract structure patterns with project-specific patterns from basepoints
  - Extract structure patterns relevant to specification writing from merged knowledge
  - Inject project-specific folder structures, component structures, module structures
  - Use project-specific architecture patterns from basepoints

- **Inject Project-Specific Testing Approaches**: Replace abstract testing approaches with project-specific testing approaches
  - Extract test patterns, strategies, and organization from merged knowledge
  - Inject project-specific test file structures, test naming conventions
  - Reference project-specific testing tools and frameworks from product tech-stack

- **Replace Abstract Placeholders**: Replace workflow and standards placeholders with project-specific content
  - Replace `{{workflows/specification/write-spec}}` with specialized workflow content
  - Replace `{{standards/*}}` with actual project-specific standards content
  - Include project-specific examples and patterns

- **Reference Project-Specific Test Strategies**: Add references to project-specific test strategies from basepoints
  - Include test patterns extracted from basepoints
  - Reference test organization structures from basepoints
  - Use project-specific test strategies as guidance in specification writing

### Step 6: Write Specialized Commands to agent-os/commands/

Write specialized commands to agent-os/commands/, replacing abstract versions:

```bash
# Ensure agent-os/commands/ directories exist
mkdir -p agent-os/commands/shape-spec/single-agent
mkdir -p agent-os/commands/write-spec/single-agent

# Write specialized shape-spec command
echo "$SPECIALIZED_SHAPE_SPEC" > agent-os/commands/shape-spec/shape-spec.md
echo "$SPECIALIZED_SHAPE_SPEC" > agent-os/commands/shape-spec/single-agent/shape-spec.md

# Write specialized shape-spec phase files
for phase_file in profiles/default/commands/shape-spec/single-agent/*.md; do
    if [ -f "$phase_file" ] && [[ "$(basename "$phase_file")" =~ ^[0-9]+- ]]; then
        PHASE_NAME=$(basename "$phase_file")
        
        # Extract specialized phase content
        PHASE_CONTENT=$(extract_phase_from_specialized "$SPECIALIZED_SHAPE_SPEC_PHASES" "$PHASE_NAME")
        
        # Write specialized phase file
        echo "$PHASE_CONTENT" > "agent-os/commands/shape-spec/single-agent/$PHASE_NAME"
    fi
done

# Write specialized write-spec command
echo "$SPECIALIZED_WRITE_SPEC" > agent-os/commands/write-spec/write-spec.md
echo "$SPECIALIZED_WRITE_SPEC" > agent-os/commands/write-spec/single-agent/write-spec.md

# Write specialized write-spec workflow (if workflows directory exists)
mkdir -p agent-os/workflows/specification
echo "$SPECIALIZED_WRITE_SPEC_WORKFLOW" > agent-os/workflows/specification/write-spec.md

echo "✅ Specialized shape-spec and write-spec commands written to agent-os/commands/"
```

Write specialized commands:
- **shape-spec.md**: Write specialized shape-spec command to `agent-os/commands/shape-spec/shape-spec.md` (replace abstract version)
- **single-agent/shape-spec.md**: Write specialized single-agent version to `agent-os/commands/shape-spec/single-agent/shape-spec.md`
- **shape-spec phase files**: Write specialized phase files (1-initialize-spec.md, 2-shape-spec.md) to `agent-os/commands/shape-spec/single-agent/`
- **write-spec.md**: Write specialized write-spec command to `agent-os/commands/write-spec/write-spec.md` (replace abstract version)
- **single-agent/write-spec.md**: Write specialized single-agent version to `agent-os/commands/write-spec/single-agent/write-spec.md`
- **write-spec workflow**: Write specialized workflow to `agent-os/workflows/specification/write-spec.md` (if needed)

Ensure specialized commands:
- Are ready to use immediately (no further processing needed)
- Reference project-specific patterns, standards, workflows
- Contain project-specific examples and patterns
- Are tailored for the specific codebase (not abstract/generic)

{{UNLESS compiled_single_command}}
## Display confirmation and next step

Once shape-spec and write-spec commands are specialized and written, output the following message:

```
✅ shape-spec and write-spec commands specialized!

- shape-spec command: Specialized with project-specific patterns, standards, and examples
- write-spec command: Specialized with project-specific structure patterns, testing approaches, and test strategies
- Abstract placeholders: Replaced with project-specific content
- Commands written to: agent-os/commands/shape-spec/ and agent-os/commands/write-spec/

Specialized commands are ready to use immediately.

NEXT STEP 👉 Run the command, `6-specialize-task-commands.md`
```
{{ENDUNLESS compiled_single_command}}

{{UNLESS standards_as_claude_code_skills}}
## User Standards & Preferences Compliance

IMPORTANT: Ensure that the specialized commands align with the user's preferences and standards as detailed in the following files:

{{standards/global/*}}
{{ENDUNLESS standards_as_claude_code_skills}}

## Specialization Functions

### Specialize Basepoints Knowledge Extraction Workflows

Replace placeholder logic in basepoints knowledge extraction workflows with project-specific patterns:

```bash
specialize_basepoints_extraction_workflows() {
    local content="$1"
    local merged_knowledge="$2"
    
    # Load project-specific extraction patterns from cache
    if [ -f "agent-os/output/deploy-agents/knowledge/basepoint-file-pattern.txt" ]; then
        BASEPOINT_FILE_PATTERN=$(cat agent-os/output/deploy-agents/knowledge/basepoint-file-pattern.txt)
    else
        BASEPOINT_FILE_PATTERN="agent-base-*.md"
    fi
    
    # Replace {{BASEPOINTS_PATH}} with actual path
    content=$(echo "$content" | sed "s|{{BASEPOINTS_PATH}}|agent-os/basepoints|g")
    
    # Replace {{BASEPOINT_FILE_PATTERN}} with project-specific pattern
    content=$(echo "$content" | sed "s|{{BASEPOINT_FILE_PATTERN}}|$BASEPOINT_FILE_PATTERN|g")
    
    # Replace extraction placeholders with project-specific extraction logic
    # Example: {{EXTRACT_PATTERNS_SECTION}} -> project-specific pattern extraction
    content=$(inject_project_extraction_logic "$content" "$merged_knowledge")
    
    # Replace {{ABSTRACTION_LAYER_NAMES}} with actual layer names from basepoints
    ABSTRACTION_LAYERS=$(extract_abstraction_layers_from_merged "$merged_knowledge")
    content=$(echo "$content" | sed "s|{{ABSTRACTION_LAYER_NAMES}}|$ABSTRACTION_LAYERS|g")
    
    echo "$content"
}
```

### Specialize Scope Detection Workflows

Replace placeholder logic in scope detection workflows with project-specific patterns:

```bash
specialize_scope_detection_workflows() {
    local content="$1"
    local merged_knowledge="$2"
    
    # Replace {{KEYWORD_EXTRACTION_PATTERN}} with project-specific keyword extraction
    KEYWORD_PATTERN=$(extract_keyword_extraction_pattern "$merged_knowledge")
    content=$(echo "$content" | sed "s|{{KEYWORD_EXTRACTION_PATTERN}}|$KEYWORD_PATTERN|g")
    
    # Replace {{SEMANTIC_ANALYSIS_PATTERN}} with project-specific semantic analysis
    SEMANTIC_PATTERN=$(extract_semantic_analysis_pattern "$merged_knowledge")
    content=$(echo "$content" | sed "s|{{SEMANTIC_ANALYSIS_PATTERN}}|$SEMANTIC_PATTERN|g")
    
    # Replace {{DETERMINE_SPEC_CONTEXT_LAYER}} with project-specific layer detection
    LAYER_DETECTION=$(extract_layer_detection_logic "$merged_knowledge")
    content=$(echo "$content" | sed "s|{{DETERMINE_SPEC_CONTEXT_LAYER}}|$LAYER_DETECTION|g")
    
    # Replace {{CALCULATE_LAYER_DISTANCE}} with project-specific distance calculation
    DISTANCE_CALC=$(extract_distance_calculation "$merged_knowledge")
    content=$(echo "$content" | sed "s|{{CALCULATE_LAYER_DISTANCE}}|$DISTANCE_CALC|g")
    
    echo "$content"
}
```

### Specialize Deep Reading Workflows

Replace placeholder logic in deep reading workflows with project-specific patterns:

```bash
specialize_deep_reading_workflows() {
    local content="$1"
    local merged_knowledge="$2"
    
    # Replace {{CODE_FILE_PATTERNS}} with project-specific file patterns
    CODE_PATTERNS=$(extract_code_file_patterns "$merged_knowledge")
    content=$(echo "$content" | sed "s|{{CODE_FILE_PATTERNS}}|$CODE_PATTERNS|g")
    
    # Replace {{FIND_MODULE_PATH}} with project-specific module path detection
    MODULE_PATH_LOGIC=$(extract_module_path_logic "$merged_knowledge")
    content=$(echo "$content" | sed "s|{{FIND_MODULE_PATH}}|$MODULE_PATH_LOGIC|g")
    
    # Replace {{EXTRACT_DESIGN_PATTERNS}} with project-specific pattern extraction
    PATTERN_EXTRACTION=$(extract_pattern_extraction_logic "$merged_knowledge")
    content=$(echo "$content" | sed "s|{{EXTRACT_DESIGN_PATTERNS}}|$PATTERN_EXTRACTION|g")
    
    # Replace {{DETECT_SIMILAR_CODE}} with project-specific similar code detection
    SIMILAR_CODE_DETECTION=$(extract_similar_code_detection "$merged_knowledge")
    content=$(echo "$content" | sed "s|{{DETECT_SIMILAR_CODE}}|$SIMILAR_CODE_DETECTION|g")
    
    echo "$content"
}
```

## Important Constraints

- Must inject project-specific patterns, standards, flows, strategies from merged knowledge
- Must replace all abstract placeholders ({{workflows/...}}, {{standards/...}}) with project-specific content
- Must replace generic examples with project-specific patterns from basepoints
- **Must specialize basepoints knowledge extraction workflows**: Replace {{BASEPOINTS_PATH}}, {{BASEPOINT_FILE_PATTERN}}, and extraction placeholders with project-specific patterns
- **Must specialize scope detection workflows**: Replace keyword extraction, semantic analysis, and layer detection placeholders with project-specific patterns
- **Must specialize deep reading workflows**: Replace code file patterns, module path detection, and pattern extraction placeholders with project-specific patterns
- Must write specialized commands to agent-os/commands/ (replace abstract versions)
- Specialized commands must be ready to use immediately (no further processing needed)
- Must preserve command structure and phase organization while injecting project-specific knowledge
